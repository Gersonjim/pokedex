📖 Pokédex Angular — Despliegue en Azure
Autor: Gerson Jimenez Arroyo
Curso: Sistemas Distribuidos
Plataforma: Azure Static Web Apps
URL pública: https://green-sky-04acf610f.7.azurestaticapps.net
Repositorio: https://github.com/Gersonjim/pokedex 

📌 Descripción del Proyecto
PokeDex es una aplicación web desarrollada con Angular que permite a los usuarios explorar diferentes especies de Pokémon, mostrando información detallada sobre sus características y habilidades. La aplicación consume la API RESTful PokéAPI para obtener los datos en tiempo real.
El proyecto fue desarrollado originalmente por Keiler Mora y puede verse en vivo en: https://keilermora.github.io/pokedex-angular/

☁️ Creación de la Cuenta en Azure for Students
Requisitos previos

Correo institucional universitario (.edu)
No se requiere tarjeta de crédito

Pasos para crear la cuenta
Paso 1 — Ingresar al portal de Azure for Students
Acceder a https://azure.microsoft.com/es-es/free/students y hacer clic en "Comenzar gratis".
Paso 2 — Iniciar sesión con cuenta institucional
Usar el correo universitario para autenticarse. Microsoft verificará automáticamente el estado estudiantil.
Paso 3 — Completar la verificación
Seguir el proceso de verificación estudiantil. Una vez aprobado, se otorgan $100 USD de crédito sin necesidad de tarjeta de crédito.
Paso 4 — Acceder al Portal de Azure
Ingresar a https://portal.azure.com con las credenciales recién creadas. El panel principal de Azure estará disponible con la suscripción Azure for Students activa.

🛠️ Tecnologías Utilizadas
TecnologíaVersiónPropósitoAngular12+Framework frontendNode.js16+Entorno de compilaciónPokéAPIv2Fuente de datos RESTAzure Static Web AppsFree tierHosting en la nubeGitHub Actions—CI/CD automatizado

🔒 Seguridad
La aplicación implementa los siguientes encabezados HTTP de seguridad, configurados mediante el archivo staticwebapp.config.json:
EncabezadoPropósitoContent-Security-PolicyPreviene ataques XSS controlando recursos cargadosStrict-Transport-SecurityObliga el uso de HTTPS en todas las conexionesX-Content-Type-OptionsEvita la detección automática de tipos MIMEX-Frame-OptionsImpide que la app sea embebida en iframes externosReferrer-PolicyMinimiza la fuga de información en cabeceras HTTPPermissions-PolicyRestringe acceso a APIs del navegador (cámara, micrófono, etc.)
Resultado del escaneo en securityheaders.com: 🟢 A

💭 Reflexión Técnica
¿Qué vulnerabilidades previenen los encabezados implementados?

Content-Security-Policy previene ataques de Cross-Site Scripting (XSS), que son uno de los vectores de ataque más comunes en aplicaciones web.
Strict-Transport-Security elimina el riesgo de ataques Man-in-the-Middle (MITM) forzando siempre HTTPS.
X-Frame-Options: DENY previene ataques de Clickjacking, donde sitios maliciosos embeben la app en iframes invisibles.
X-Content-Type-Options: nosniff evita que el navegador interprete archivos con tipos MIME incorrectos, previniendo ataques de MIME sniffing.

¿Qué aprendí sobre la relación entre despliegue y seguridad web?
El despliegue no termina cuando la aplicación está en línea. La configuración de encabezados HTTP de seguridad es una capa fundamental de protección que muchos desarrolladores omiten. Aprendí que herramientas como securityheaders.com permiten evaluar objetivamente el nivel de seguridad de una aplicación y que con pocos cambios de configuración se puede pasar de una calificación F a una A.
¿Qué desafíos encontré en el proceso?
El principal desafío fue un error de policy violation al crear la Static Web App en Azure. La región East US 2 estaba bloqueada por las políticas de la suscripción estudiantil. La solución fue cambiar la región a West US 2, lo que resolvió el problema inmediatamente. Este tipo de restricciones son comunes en entornos cloud corporativos y estudiantiles, y aprendí la importancia de conocer las limitaciones de la plataforma antes de iniciar un despliegue.

📎 Referencias

Keiler Mora — Autor original del proyecto
PokéAPI — API RESTful de Pokémon
Azure Static Web Apps Documentation
securityheaders.com
OWASP — Security Headers
