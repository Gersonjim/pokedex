# 🚀 Despliegue.md — Proceso de Despliegue en Azure Static Web Apps

**Autor:** Gerson Jimenez Arroyo  
**Fecha:** Abril 2026  
**Plataforma:** Microsoft Azure Static Web Apps  
**URL desplegada:** https://green-sky-04acf610f.7.azurestaticapps.net

---

## 📋 Resumen del Proceso

| Fase | Descripción | Estado |
|------|------------|--------|
| 1 | Compilación del proyecto Angular | ✅ Completado |
| 2 | Creación del repositorio en GitHub | ✅ Completado |
| 3 | Subida del código compilado | ✅ Completado |
| 4 | Creación de Azure Static Web App | ✅ Completado |
| 5 | Configuración de headers de seguridad | ✅ Completado |
| 6 | Corrección de errores CSP | ✅ Completado |
| 7 | Diagnóstico de imágenes bloqueadas | ✅ Documentado |
| 8 | Verificación en securityheaders.com | ✅ Completado — Calificación: A |

---

## ⚙️ Fase 1 — Compilación del Proyecto Angular

### Requisitos previos instalados
- Node.js v16+
- npm

### Comandos ejecutados

**1. Navegar a la carpeta del proyecto:**
```bash
cd C:\Escritorio\pokedex\pokedex-angular
```

**2. Instalar dependencias:**
```bash
npm install
```

**Resultado:**
```
added 1224 packages, and audited 1225 packages in 38s
170 packages are looking for funding
```

> ⚠️ **Advertencias recibidas (no son errores):**
> - `npm warn deprecated source-map-resolve@0.6.0` — paquete deprecado, no afecta la funcionalidad
> - `fatal: not a git repository` — error de husky al no encontrar repositorio Git, no afecta el build
> - `97 vulnerabilities` — vulnerabilidades en dependencias de desarrollo, no afectan producción

**3. Compilar para producción:**
```bash
npm run build
```

**Resultado:** Se generó exitosamente la carpeta:
```
C:\Escritorio\pokedex\pokedex-angular\dist\pokedex-angular\
```

---

## 📁 Fase 2 — Creación del Repositorio en GitHub

1. Ingresar a https://github.com y crear nuevo repositorio
2. Configuración usada:
   - **Nombre:** `pokedex`
   - **Visibilidad:** Public
   - **Initialize with README:** ✅ Sí
   - **.gitignore:** None
   - **License:** None
3. Repositorio creado exitosamente

---

## 📤 Fase 3 — Subida del Código a GitHub

### ❌ Error — Límite de archivos en GitHub Web

Al intentar arrastrar los archivos desde la interfaz web de GitHub apareció el siguiente error:

> *"Yowza, that's a lot of files. Try uploading fewer than 100 at a time."*

**Causa:** La carpeta `dist/pokedex-angular/` contenía más de 100 archivos, superando el límite de la interfaz web.

**Solución:** Usar **GitHub Desktop** para subir todos los archivos sin restricción de cantidad.

### Pasos con GitHub Desktop

**1. Instalar GitHub Desktop** desde https://desktop.github.com

**2. Clonar el repositorio:**
- File → Clone a repository
- Seleccionar `pokedex`
- Local path: `C:\Escritorio\pokedex-repo\`

**3. Copiar archivos compilados:**
- Origen: `C:\Escritorio\pokedex\pokedex-angular\dist\pokedex-angular\`
- Destino: `C:\Escritorio\pokedex-repo\`
- Seleccionar todo `CTRL+A` → Copiar `CTRL+C` → Pegar `CTRL+V`

**4. Hacer commit y push:**
- Summary: `Add compiled PokeDex Angular app`
- Clic en **"Commit to main"**
- Clic en **"Push origin"**

✅ Archivos subidos correctamente al repositorio.

---

## ☁️ Fase 4 — Creación de Azure Static Web App

### ❌ Error — Policy Violation en Azure

Al intentar crear el recurso con la región **East US 2**, Azure mostró el siguiente error:

> *"The template deployment failed because of policy violation. Please see details for more information."*

**Causa:** La suscripción Azure for Students tiene restricciones de política que bloquean ciertas regiones geográficas.

**Solución:** Cambiar la región de `East US 2` a **`West US 2`**. El recurso se creó exitosamente.

### Configuración final del recurso

| Campo | Valor usado |
|-------|------------|
| Subscription | Azure for Students |
| Resource Group | `rg-pokedex` (nuevo) |
| Name | `pokedex-app` |
| Plan type | Free |
| Region | **West US 2** |
| Source | GitHub |
| Repository | `pokedex` |
| Branch | `main` |
| Build Preset | Custom |
| App location | `/` |
| Output location | `/` |

Azure creó automáticamente un **GitHub Action** que despliega la aplicación en cada push a `main`. El workflow mostró estado ✅ verde al completarse.

**URL pública asignada:** https://green-sky-04acf610f.7.azurestaticapps.net

---

## 🔒 Fase 5 — Configuración de Headers de Seguridad y Corrección de Errores

Esta fue la fase más iterativa del proceso. Se requirieron múltiples ajustes al CSP para lograr que la app funcionara correctamente con buena seguridad.

### ❌ Error — Google Fonts y PokéAPI GraphQL bloqueados

La consola del navegador (F12) mostró estos errores:

```
Loading the stylesheet 'https://fonts.googleapis.com/css2?family=Roboto&display=swap' 
violates Content Security Policy directive: "style-src 'self' 'unsafe-inline'"

Connecting to 'https://beta.pokeapi.co/graphql/v1beta' violates 
Content Security Policy directive: "connect-src 'self' https://pokeapi.co"
```

**Causa:** El CSP no incluía los dominios de Google Fonts ni de la API GraphQL de PokéAPI.

**Solución:** Agregar los dominios faltantes al CSP:
- `https://fonts.googleapis.com` → en `style-src`
- `https://fonts.gstatic.com` → en `font-src`
- `https://beta.pokeapi.co` → en `connect-src`

---

### ❌ Error — Imágenes de Pokémon bloqueadas por CSP

La consola mostró:

```
Loading the image 'https://assets.pokemon.com/assets/cms2/img/pokedex/full/003.png' 
violates Content Security Policy directive: "img-src 'self' data: https://raw.githubusercontent.com https://pokeapi.co"
```

**Causa:** Las imágenes de los Pokémon provienen de `assets.pokemon.com`, dominio no incluido en `img-src`.

**Solución:** Cambiar `img-src` a `https:` para permitir imágenes de cualquier dominio HTTPS, dando mayor flexibilidad.

---

### ❌ Error — HTTP 500 al intentar A+

Al intentar lograr calificación A+ eliminando `'unsafe-inline'` del `script-src`, la aplicación devolvió error HTTP 500 y dejó de funcionar completamente.

**Causa:** Angular requiere `'unsafe-inline'` en `script-src` para inyectar sus scripts de inicialización en tiempo de ejecución.

**Solución:** Restaurar `'unsafe-inline'` en `script-src` y mantener la calificación A.

---

### ⚠️ Observación — Imágenes bloqueadas en red universitaria

Durante las pruebas en la red de la universidad, las imágenes de los Pokémon no cargaban con el error:

```
Failed to load resource: net::ERR_CONNECTION_RESET
```

**Causa:** El firewall institucional de la universidad bloquea el dominio `assets.pokemon.com` por ser un sitio de entretenimiento. Este comportamiento es una política de seguridad de red completamente normal en entornos académicos.

**Verificación:** Al acceder a la aplicación desde una red doméstica o datos móviles, las imágenes cargan correctamente, confirmando que el problema es exclusivo de la red universitaria y no del despliegue.

> **Conclusión técnica:** Este tipo de restricciones de red son independientes del código y del despliegue. La aplicación funciona correctamente en cualquier red sin restricciones institucionales.

---

### ✅ Configuración final funcionando

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
    "rewrite": "/index.html"
  }
}
```

---

## ✅ Fase 6 — Verificación Final

### Prueba en securityheaders.com

- **URL escaneada:** https://green-sky-04acf610f.7.azurestaticapps.net
- **Calificación obtenida:** 🟢 **A**
- **Headers verificados:**
  - ✅ Content-Security-Policy
  - ✅ Strict-Transport-Security
  - ✅ X-Content-Type-Options
  - ✅ X-Frame-Options
  - ✅ Referrer-Policy
  - ✅ Permissions-Policy

### Validaciones finales

| Validación | Resultado |
|-----------|-----------|
| App carga correctamente desde URL pública | ✅ |
| Imágenes visibles en red doméstica | ✅ |
| HTTPS activo | ✅ |
| Sin errores 404/500 | ✅ |
| Calificación en securityheaders.com | ✅ A |

---

## 📎 Herramientas Utilizadas

| Herramienta | Uso |
|------------|-----|
| Node.js + npm | Compilación del proyecto Angular |
| GitHub Desktop | Subida masiva de archivos al repositorio |
| Azure Portal | Creación y configuración del Static Web App |
| GitHub Actions | CI/CD automatizado |
| Chrome DevTools (F12) | Diagnóstico de errores CSP en consola |
| securityheaders.com | Verificación de headers de seguridad |
