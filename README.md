# 📖 Pokédex Angular — Despliegue en Azure

**Autor:** Gerson Jimenez Arroyo  
**Curso:** Sistemas Distribuidos  
**Plataforma:** Azure Static Web Apps  
**URL pública:** https://green-sky-04acf610f.7.azurestaticapps.net  
**Repositorio:** https://github.com/Gersonjim/pokedex

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
| `Referrer-Policy` | Necesario para cargar imágenes de assets.pokemon.com |
| `Permissions-Policy` | Restringe acceso a APIs del navegador |

### Configuración final (`staticwebapp.config.json`)

```json
{
  "globalHeaders": {
    "Content-Security-Policy": "default-src 'self'; script-src 'self' 'unsafe-inline'; style-src 'self' 'unsafe-inline' https://fonts.googleapis.com; img-src 'self' data: blob: https:; connect-src 'self' https://pokeapi.co https://beta.pokeapi.co; font-src 'self' https://fonts.gstatic.com; object-src 'none'; base-uri 'self'; form-action 'self';",
    "Strict-Transport-Security": "max-age=31536000; includeSubDomains; preload",
    "X-Content-Type-Options": "nosniff",
    "X-Frame-Options": "DENY",
    "Referrer-Policy": "unsafe-url",
    "Permissions-Policy": "geolocation=(), microphone=(), camera=()"
  },
  "navigationFallback": {
    "rewrite": "/index.html",
    "exclude": [
      "/pokedex-angular/*",
      "/assets/*",
      "/*.png",
      "/*.gif",
      "/*.jpg",
      "/*.svg",
      "/*.ico",
      "/*.woff",
      "/*.woff2",
      "/*.js",
      "/*.css",
      "/*.txt"
    ]
  }
}
```

**Resultado del escaneo en securityheaders.com: 🟢 A**

> **Nota técnica sobre los warnings:**
> - `unsafe-inline` en `script-src`: Angular lo requiere obligatoriamente para sus scripts de inicialización. Sin esta directiva la app devuelve error HTTP 500.
> - `unsafe-url` en `Referrer-Policy`: Necesario para que las imágenes de `assets.pokemon.com` carguen correctamente. Sin este valor las imágenes son bloqueadas.
> Ambos son compromisos técnicos necesarios para el correcto funcionamiento de la aplicación.

---

## 💭 Reflexión Técnica

### ¿Qué vulnerabilidades previenen los encabezados implementados?

- **Content-Security-Policy** previene ataques XSS (Cross-Site Scripting), definiendo exactamente qué dominios pueden proveer scripts, estilos, imágenes y conexiones.
- **Strict-Transport-Security** elimina el riesgo de ataques Man-in-the-Middle (MITM) forzando siempre HTTPS.
- **X-Frame-Options: DENY** previene ataques de Clickjacking donde sitios maliciosos embeben la app en iframes invisibles.
- **X-Content-Type-Options: nosniff** evita que el navegador interprete archivos con tipos MIME incorrectos.
- **Referrer-Policy** controla qué información de origen se comparte al navegar hacia otros sitios.
- **Permissions-Policy** restringe el acceso a APIs sensibles del navegador como cámara, micrófono y geolocalización.

### ¿Qué aprendí sobre la relación entre despliegue y seguridad web?

El despliegue no termina cuando la aplicación está en línea. Durante este proceso aprendí que la seguridad y la funcionalidad deben balancearse cuidadosamente. Uno de los aprendizajes más importantes fue entender cómo el `navigationFallback` de Azure Static Web Apps puede interferir con la carga de archivos estáticos si no se configura correctamente el campo `exclude`.

También aprendí que el Content-Security-Policy es el encabezado más complejo de configurar, ya que requiere conocer exactamente todos los recursos externos que usa la aplicación.

### ¿Qué desafíos encontré en el proceso?

**Desafío 1 — Policy Violation en Azure:**  
Al crear la Static Web App con la región `East US 2`, Azure rechazó el despliegue. Se resolvió cambiando la región a `West US 2`.

**Desafío 2 — Límite de archivos en GitHub:**  
La carpeta compilada superó el límite de 100 archivos de la interfaz web. Se resolvió usando GitHub Desktop.

**Desafío 3 — Google Fonts, PokéAPI GraphQL e imágenes bloqueadas por CSP:**  
Múltiples recursos externos estaban bloqueados. Se identificaron mediante F12 y se agregaron al CSP.

**Desafío 4 — Error HTTP 500 al intentar A+:**  
Al eliminar `unsafe-inline` la app dejó de funcionar. Angular requiere esta directiva.

**Desafío 5 — Imágenes de sprites no cargaban (navigationFallback):**  
Los sprites locales de las tarjetas daban 404 porque el `navigationFallback` redirigía todas las peticiones a `index.html`, incluyendo las imágenes. Se resolvió agregando las extensiones de archivos estáticos en el campo `exclude` del `navigationFallback`.

**Desafío 6 — Carpeta pokedex-angular/assets/images/ faltante:**  
La app buscaba los sprites en `/pokedex-angular/assets/images/` pero esa carpeta no existía en el repo. Se creó manualmente y se subió via GitHub Desktop.

---

## 📎 Referencias

- [Keiler Mora — Autor original del proyecto](https://github.com/keilermora/pokedex-angular)
- [PokéAPI — API RESTful de Pokémon](https://pokeapi.co/)
- [Azure Static Web Apps Documentation](https://docs.microsoft.com/azure/static-web-apps/)
- [securityheaders.com](https://securityheaders.com/)
- [OWASP — Security Headers](https://owasp.org/www-project-secure-headers/)
- [MDN — Content Security Policy](https://developer.mozilla.org/es/docs/Web/HTTP/CSP)

