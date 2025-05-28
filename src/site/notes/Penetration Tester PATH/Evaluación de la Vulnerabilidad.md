---
{"-publish":true,"permalink":"/penetration-tester-path/evaluacion-de-la-vulnerabilidad/","PassFrontmatter":true}
---


----
#vulnerability #security #patch #mitigation #red-team

--- 
# Evaluación de Seguridad
## Propósito:

- Identificar y confirmar **vulnerabilidades** para `parchear`, `mitigar` o `eliminarlas`


### Tipos y metodologías:

- **Varían** según las **necesidades** de la organización (`redes`, `sistemas`, `aplicaciones`)
- Algunas empresas requieren pruebas avanzadas (`Red team`), mientras que otras se enfocan en medidas básicas

#### Enfoque adaptativo:

- Cada organización tiene **requisitos de cumplimiento**, **tolerancia al riesgo** y **modelos de negocio distintos**.
- Deben gestionar tanto **vulnerabilidades** antiguas (`legacy`) como nuevas amenazas

---
# Evaluación de la vulnerabilidad 

`Vulnerability assessments` son útiles para todo tipo de organizaciones, sin importar su tamaño o sector. Estas evaluaciones comparan los `sistemas` y `redes` contra estándares de seguridad establecidos, como listas de verificación o normativas específicas.

El tipo de **estándar** aplicado depende de **varios factores**, como las regulaciones de la industria, el tamaño de la `red` de la empresa, las `aplicaciones` que usan o `desarrollan`, y su nivel de madurez en `seguridad`.

Estas evaluaciones pueden hacerse por sí solas o combinarse con otros tipos de pruebas de seguridad, dependiendo de lo que necesite cada organización. Su objetivo principal es detectar fallos de seguridad para cumplir con los requisitos y reducir riesgos.

---

# Prueba De Penetración 

1. `Black Box Pentesting:`
	Enfoque: Simula un atacante externo sin conocimiento previo de la red o sistemas.

	Acceso: Solo se proporciona el nombre de la empresa (externo) o un puerto de red (interno).

	Objetivo: Descubrir vulnerabilidades desde cero, como lo haría un hacker real.

	Usado por: Terceros para evaluaciones externas.
---
2. `Grey Box Pentesting:`
	Enfoque: Simula un empleado no técnico (ej: recepcionista) con conocimiento limitado.

	Acceso: Se proporcionan rangos de IP o direcciones específicas.

	Objetivo: Evaluar riesgos desde una perspectiva interna con información parcial.
---
3. `White Box Pentesting:`
	Enfoque: Auditoría completa con acceso total a sistemas, configuraciones y código fuente.

	Acceso: Documentación técnica, credenciales y permisos administrativos.

	Objetivo: Identificar vulnerabilidades difíciles de detectar sin información previa.

---
## Especialista en Pentesting

- `Application Pentesters:`
	Evalúan aplicaciones web, APIs, móviles y cliente-servidor.

	Expertos en análisis de código fuente (**white box**) y pruebas ciegas (**black box**).

---

- `Network/Infrastructure Pentesters:`
	Analizan redes, dispositivos (`routers`, `firewalls`), servidores y `Active Directory`.

	Requieren conocimientos en Linux, Windows, scripting y herramientas como Nessus.

	Usan técnicas evasivas, no evasivas o híbridas.

---

##### Physical Pentesters:

- Explotan fallas en seguridad física:

	Puertas abiertas.
	
	Acceso no autorizado a centros de datos.
	
	Técnicas de `tailgating` (colarse detrás de un empleado).

---
##### Social Engineering Pentesters:

- Engañan a empleados mediante:

	`Phishing` (correos falsos).

    `Vishing` (llamadas fraudulentas).

    `Spoofing` (hacerse pasar por personal autorizado).

---

###### Conclusión:

 Cada tipo de `pentesting` (**black**, **grey-white box**) tiene un propósito distinto según el nivel de información disponible.

 Los `pentesters` suelen especializarse (**aplicaciones, redes, física, ingeniería social**).

 Herramientas como `Nessus` son útiles, **==pero no reemplazan el "análisis humano"==**.

 El objetivo final es identificar y corregir vulnerabilidades antes de que un atacante real las explote.

---
#Hacking, #vulnerabilidades, #red, #aplicaciones, #seguridad #física, #ingenieria-social

---