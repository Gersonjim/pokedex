# 🚀 Despliegue.md — Proceso de Despliegue en Azure Static Web Apps

**Autor:** Gerson Jimenez Arroyo  
**Fecha:** Mayo 2026  
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
| 6 | Corrección de recursos bloqueados por CSP | ✅ Completado |
| 7 | Corrección de sprites bloqueados por navigationFallback | ✅ Completado |
| 8 | Verificación en securityheaders.com | ✅ Completado — Calificación: A |

---

## ⚙️ Fase 1 — Compilación del Proyecto Angular

**1. Inicializar Git y navegar a la carpeta:**
```bash
cd "C:\Users\GERSON JIMENEZ\Downloads\pokedex-angular"
git init
npm install
npm run build
```

**Resultado:** Carpeta generada exitosamente:
```
dist\pokedex-angular\
```

---

## 📁 Fase 2 — Creación del Repositorio en GitHub

| Campo | Valor |
|-------|-------|
| Nombre | `pokedex` |
| Visibilidad | Public |
| Initialize with README | ✅ Sí |

---

## 📤 Fase 3 — Subida del Código a GitHub

### ❌ Error — Límite de archivos en GitHub Web
> *"Yowza, that's a lot of files. Try uploading fewer than 100 at a time."*

**Solución:** Usar GitHub Desktop para subir sin límite.

1. Clonar repo → Local path: `C:\Escritorio\pokedex-repo\`
2. Copiar contenido de `dist\pokedex-angular\` al repo
3. Commit: `Add compiled PokeDex Angular app` → Push

---

## ☁️ Fase 4 — Creación de Azure Static Web App

### ❌ Error — Policy Violation en Azure
> *"The template deployment failed because of policy violation."*

**Causa:** Región `East US 2` bloqueada por políticas estudiantiles.  
**Solución:** Cambiar región a `West US 2`.

| Campo | Valor |
|-------|-------|
| Resource Group | `rg-pokedex` |
| Name | `pokedex-app` |
| Plan | Free |
| Region | West US 2 |
| Repository | `pokedex` |
| Branch | `main` |
| App location | `/` |
| Output location | `/` |

**URL asignada:** https://green-sky-04acf610f.7.azurestaticapps.net

---

## 🔒 Fase 5 — Configuración de Headers y Corrección de Errores

### ❌ Error — Google Fonts y PokéAPI GraphQL bloqueados
**Causa:** CSP no incluía dominios externos de la app.  
**Solución:** Agregar `fonts.googleapis.com`, `fonts.gstatic.com` y `beta.pokeapi.co`.

### ❌ Error — Imágenes de Pokémon grandes bloqueadas
**Causa:** `assets.pokemon.com` no estaba en `img-src`.  
**Solución:** Cambiar `img-src` a `https:` para permitir cualquier dominio HTTPS.

### ❌ Error — HTTP 500 al intentar A+
**Causa:** Angular requiere `unsafe-inline` en `script-src`.  
**Solución:** Restaurar `unsafe-inline` y mantener calificación A.

### ❌ Error — Sprites de tarjetas no cargaban (404)
Este fue el error más complejo del proceso. Los sprites pequeños de las tarjetas de Pokémon daban error 404 aunque los archivos existían en el repositorio.

**Causa:** El `navigationFallback` de Azure redirigía **todas** las peticiones a `index.html`, incluyendo las peticiones de imágenes. Cuando la app pedía:
```
/pokedex-angular/assets/images/pokemon-green.png
```
Azure respondía con `index.html` en lugar de la imagen.

**Solución:** Agregar el campo `exclude` en `navigationFallback` para excluir archivos estáticos:
```json
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
```

### ❌ Error — Carpeta pokedex-angular/assets/images/ faltante
**Causa:** Los sprites se buscaban en `/pokedex-angular/assets/images/` pero solo existía `/assets/images/`.  
**Solución:** Crear la carpeta `pokedex-angular\assets\images\` en el repo y copiar todos los archivos de imágenes.

---

## ✅ Configuración Final — staticwebapp.config.json

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

---

## ✅ Fase 6 — Verificación Final

| Verificación | Resultado |
|-------------|-----------|
| App carga correctamente | ✅ |
| Sprites de Pokémon visibles | ✅ |
| Imágenes grandes de detalle visibles | ✅ |
| HTTPS activo | ✅ |
| Sin errores 404/500 | ✅ |
| Calificación securityheaders.com | ✅ A |

---

## 📎 Herramientas Utilizadas

| Herramienta | Uso |
|------------|-----|
| Node.js + npm | Compilación del proyecto Angular |
| GitHub Desktop | Subida masiva de archivos |
| Azure Portal | Creación del Static Web App |
| GitHub Actions | CI/CD automatizado |
| Chrome DevTools (F12) | Diagnóstico de errores CSP |
| securityheaders.com | Verificación de headers de seguridad |
