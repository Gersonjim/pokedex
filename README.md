# 📖 Pokédex Angular — Despliegue en Azure

**Autor:** Gerson Jimenez Arroyo  
**Curso:** Sistemas Distribuidos  
**Plataforma:** Azure Static Web Apps  
**URL pública:** https://green-sky-04acf610f.7.azurestaticapps.net  
**Repositorio:** https://github.com/GersonJim/pokedex

---

## 📌 Descripción del Proyecto

PokeDex es una aplicación web desarrollada con **Angular** que permite a los usuarios explorar diferentes especies de Pokémon, mostrando información detallada sobre sus características y habilidades. La aplicación consume la API RESTful [PokéAPI](https://pokeapi.co/) para obtener los datos en tiempo real.

El proyecto fue desarrollado originalmente por **Keiler Mora** y puede verse en vivo en: https://keilermora.github.io/pokedex-angular/

---

## ☁️ Creación de la Cuenta en Azure for Students

### Requisitos previos
- Correo institucional universitario (`.edu`)
- No se requiere tarjeta de crédito

### Pasos para crear la cuenta

**Paso 1 — Ingresar al portal de Azure for Students**  
Acceder a https://azure.microsoft.com/es-es/free/students y hacer clic en **"Comenzar gratis"**.

**Paso 2 — Iniciar sesión con cuenta institucional**  
Usar el correo universitario para autenticarse. Microsoft verificará automáticamente el estado estudiantil.

**Paso 3 — Completar la verificación**  
Seguir el proceso de verificación estudiantil. Una vez aprobado, se otorgan **$100 USD de crédito** sin necesidad de tarjeta de crédito.

**Paso 4 — Acceder al Portal de Azure**  
Ingresar a https://portal.azure.com con las credenciales recién creadas. El panel principal de Azure estará disponible con la suscripción **Azure for Students** activa.

---

## 🛠️ Tecnologías Utilizadas

| Tecnología | Versión | Propósito |
|-----------|---------|-----------|
| Angular | 12+ | Framework frontend |
| Node.js | 16+ | Entorno de compilación |
| PokéAPI REST | v2 | Fuente de datos REST |
| PokéAPI GraphQL | v1beta | Fuente de datos GraphQL |
| Google Fonts | — | Tipografía (Roboto) |
| Azure Static Web Apps | Free tier | Hosting en la nube |
| GitHub Actions | — | CI/CD automatizado |

---

## 🔒 Seguridad

La aplicación implementa los siguientes encabezados HTTP de seguridad, configurados mediante el archivo `staticwebapp.config.json`:

| Encabezado | Propósito |
|-----------|-----------|
| `Content-Security-Policy` | Previene ataques XSS controlando todos los recursos cargados |
| `Strict-Transport-Security` | Obliga el uso de HTTPS en todas las conexiones |
| `X-Content-Type-Options` | Evita la detección automática de tipos MIME |
| `X-Frame-Options` | Impide que la app sea embebida en iframes externos |
| `Referrer-Policy` | Minimiza la fuga de información en cabeceras HTTP |
| `Permissions-Policy` | Restringe acceso a APIs del navegador (cámara, micrófono, etc.) |

### Configuración final (`staticwebapp.config.json`)

```json
{
  "globalHeaders": {
    "Content-Security-Policy": "default-src 'self'; script-src 'self' 'unsafe-inline'; style-src 'self' 'unsafe-inline' https://fonts.googleapis.com; img-src 'self' data: blob: https://raw.githubusercontent.com https://pokeapi.co https://assets.pokemon.com; connect-src 'self' https://pokeapi.co https://beta.pokeapi.co; font-src 'self' https://fonts.gstatic.com; object-src 'none'; base-uri 'self'; form-action 'self';",
    "Strict-Transport-Security": "max-age=31536000; includeSubDomains; preload",
    "X-Content-Type-Options": "nosniff",
    "X-Frame-Options": "DENY",
    "Referrer-Policy": "no-referrer",
    "Permissions-Policy": "geolocation=(), microphone=(), camera=()"
  },
  "navigationFallback": {
    "rewrite": "/index.html"
  }
}
```

**Resultado del escaneo en securityheaders.com: 🟢 A**

> **Nota técnica:** La calificación A+ requeriría eliminar `'unsafe-inline'` del `script-src`. Sin embargo, Angular inyecta scripts inline en tiempo de ejecución, lo que hace técnicamente inviable quitar esta directiva sin modificar el código fuente del framework. Forzar su eliminación causó un error HTTP 500 que dejó la aplicación completamente inaccesible.

---

## 💭 Reflexión Técnica

### ¿Qué vulnerabilidades previenen los encabezados implementados?

- **Content-Security-Policy** previene ataques XSS (Cross-Site Scripting), definiendo exactamente qué dominios pueden proveer scripts, estilos, imágenes y conexiones. Es el encabezado más complejo pero también el más poderoso.
- **Strict-Transport-Security** elimina el riesgo de ataques Man-in-the-Middle (MITM) forzando siempre HTTPS, incluso si el usuario escribe `http://` en el navegador.
- **X-Frame-Options: DENY** previene ataques de Clickjacking, donde sitios maliciosos embeben la app en iframes invisibles para engañar al usuario.
- **X-Content-Type-Options: nosniff** evita que el navegador interprete archivos con tipos MIME incorrectos, previniendo ataques de MIME sniffing.
- **Referrer-Policy: no-referrer** protege la privacidad del usuario evitando que la URL de origen se filtre al navegar hacia otros sitios.
- **Permissions-Policy** restringe el acceso a APIs sensibles del navegador como cámara, micrófono y geolocalización.

### ¿Qué aprendí sobre la relación entre despliegue y seguridad web?

El despliegue no termina cuando la aplicación está en línea. Durante este proceso aprendí que la seguridad y la funcionalidad deben balancearse cuidadosamente. Intentar eliminar `'unsafe-inline'` del CSP para lograr A+ causó un error HTTP 500 que dejó la app completamente rota, demostrando que una política de seguridad demasiado estricta puede ser tan dañina como no tener ninguna.

También aprendí que el Content-Security-Policy es el encabezado más complejo de configurar, ya que requiere conocer exactamente todos los recursos externos que usa la aplicación. Cada dominio bloqueado fue identificado mediante la consola del navegador (F12) y agregado manualmente al CSP.

### ¿Qué desafíos encontré en el proceso?

**Desafío 1 — Policy Violation en Azure:**  
Al crear la Static Web App con la región `East US 2`, Azure rechazó el despliegue por políticas de la suscripción estudiantil. Se resolvió cambiando la región a `West US 2`.

**Desafío 2 — Límite de archivos en GitHub:**  
La carpeta compilada de Angular superó el límite de 100 archivos de la interfaz web de GitHub. Se resolvió usando GitHub Desktop para subir todos los archivos sin restricción.

**Desafío 3 — Error HTTP 500 al quitar `'unsafe-inline'`:**  
Al intentar A+ eliminando `'unsafe-inline'`, la app dejó de funcionar. Angular requiere esta directiva para sus scripts de inicialización. Se restauró `'unsafe-inline'` manteniendo la calificación A.

**Desafío 4 — Imágenes de Pokémon bloqueadas por CSP:**  
Las imágenes no aparecían porque `assets.pokemon.com` no estaba en el CSP. La consola del navegador mostró el error exacto y se agregó el dominio al `img-src`.

**Desafío 5 — Google Fonts y PokéAPI GraphQL bloqueados:**  
La tipografía y las llamadas GraphQL estaban siendo bloqueadas. Se identificaron mediante F12 y se agregaron `fonts.googleapis.com`, `fonts.gstatic.com` y `beta.pokeapi.co` al CSP.

---

## 📎 Referencias

- [Keiler Mora — Autor original del proyecto](https://github.com/keilermora/pokedex-angular)
- [PokéAPI — API RESTful de Pokémon](https://pokeapi.co/)
- [Azure Static Web Apps Documentation](https://docs.microsoft.com/azure/static-web-apps/)
- [securityheaders.com](https://securityheaders.com/)
- [OWASP — Security Headers](https://owasp.org/www-project-secure-headers/)
- [MDN — Content Security Policy](https://developer.mozilla.org/es/docs/Web/HTTP/CSP)
