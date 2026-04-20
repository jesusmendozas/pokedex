# 🚀 Despliegue — Pokédex Angular en Azure Static Web Apps

## 📋 Índice

1. [Requisitos previos](#1--requisitos-previos)
2. [Preparación del repositorio](#2--preparación-del-repositorio)
3. [Creación del recurso en Azure](#3--creación-del-recurso-en-azure)
4. [Configuración del despliegue](#4--configuración-del-despliegue)
5. [Configuración de seguridad](#5--configuración-de-seguridad)
6. [Errores encontrados y soluciones](#6--errores-encontrados-y-soluciones)
7. [Verificación final](#7--verificación-final)

---

## 1. 🔧 Requisitos Previos

- Cuenta activa en **Azure for Students**.
- Repositorio del proyecto subido a **GitHub**.
- **Node.js v18+** y **Angular CLI** instalados localmente.

---

## 2. 📁 Preparación del Repositorio

### Estructura del repositorio

El repositorio contiene la aplicación Angular dentro de una subruta:

```
pokedex/
└── sistemas-distribuidos/
    └── poke-dex-lab/
        └── source/
            └── pokedex-angular/    ← Proyecto Angular
                ├── src/
                ├── angular.json
                ├── package.json
                ├── staticwebapp.config.json
                └── ...
```

### Verificar que compila correctamente

```bash
cd sistemas-distribuidos/poke-dex-lab/source/pokedex-angular
npm install
npm run build


## 3. ☁️ Creación del Recurso en Azure

### Paso 1 — Acceder al portal

1. Ingresar a [https://portal.azure.com](https://portal.azure.com).
2. En la barra de búsqueda, escribir **"Static Web Apps"** y seleccionar el servicio.

### Paso 2 — Crear un nuevo recurso

1. Clic en **"+ Crear"**.
2. Completar los datos del formulario:

| Campo | Valor |
|---|---|
| **Suscripción** | Azure for Students |
| **Grupo de recursos** | pokedex-sistemas-distribuidos
| **Nombre** | pokedex
| **Tipo de plan** | Gratis (Free) |
| **Región** | East US 2 
| **Origen de implementación** | GitHub |

Al finalizar la creación, Azure genera automáticamente un archivo de **GitHub Actions**  en el repositorio. Este workflow se ejecuta en cada `git push` a la rama configurada y realiza:

1. Checkout del código.
2. Instalación de dependencias (`npm install`).
3. Build de producción (`npm run build`).
4. Despliegue automático al recurso de Azure Static Web Apps.

El pipeline se ejecuta automáticamente. El progreso se puede monitorear en la pestaña **Actions** del repositorio en GitHub.

## 5. 🔒 Configuración de Seguridad

### Archivo `staticwebapp.config.json`

Este archivo se ubica en la raíz del proyecto Angular y configura el comportamiento de Azure Static Web Apps:

```json
{
  "navigationFallback": {
    "rewrite": "/index.html"
  },
  "globalHeaders": {
    "Content-Security-Policy": "default-src 'self'; script-src 'self' 'unsafe-inline'; style-src 'self' 'unsafe-inline' https://fonts.googleapis.com; img-src 'self' data: https://raw.githubusercontent.com; font-src 'self' https://fonts.gstatic.com; connect-src 'self' https://pokeapi.co https://beta.pokeapi.co; object-src 'none'; base-uri 'self'; form-action 'self';",
    "Strict-Transport-Security": "max-age=31536000; includeSubDomains; preload",
    "X-Content-Type-Options": "nosniff",
    "X-Frame-Options": "SAMEORIGIN",
    "Referrer-Policy": "strict-origin-when-cross-origin",
    "Permissions-Policy": "camera=(), microphone=(), geolocation=(), interest-cohort=()",
    "X-XSS-Protection": "1; mode=block",
    "Cross-Origin-Opener-Policy": "same-origin"
  }
}
```

### Detalle de las directivas CSP

| Directiva | Configuración | Justificación |
|---|---|---|
| `default-src` | `'self'` | Solo permite recursos del mismo origen por defecto |
| `script-src` | `'self' 'unsafe-inline'` | Permite scripts propios y los inline generados por Angular en el build |
| `style-src` | `'self' 'unsafe-inline' https://fonts.googleapis.com` | Permite estilos propios, inline de Angular y Google Fonts |
| `img-src` | `'self' data: https://raw.githubusercontent.com` | Permite imágenes propias, data URIs y sprites de GitHub |
| `font-src` | `'self' https://fonts.gstatic.com` | Permite fuentes propias y archivos de Google Fonts |
| `connect-src` | `'self' https://pokeapi.co https://beta.pokeapi.co` | Permite llamadas HTTP/GraphQL a PokéAPI |
| `object-src` | `'none'` | Bloquea plugins como Flash |
| `base-uri` | `'self'` | Previene inyección de `<base>` |
| `form-action` | `'self'` | Restringe destinos de formularios |

### Resultado en SecurityHeaders.com

La configuración obtiene una calificación **A** en [securityheaders.com](https://securityheaders.com/), implementando protección contra:

- **XSS** (Cross-Site Scripting) — mediante CSP y X-XSS-Protection.
- **Clickjacking** — mediante X-Frame-Options.
- **MIME-type sniffing** — mediante X-Content-Type-Options.
- **Downgrade attacks** — mediante HSTS.
- **Information leakage** — mediante Referrer-Policy.

---

## 6. 🐛 Errores Encontrados y Soluciones

### Error 1 — Imágenes de Pokémon devuelven 404

**Síntoma:**
```
GET https://jolly-dune-...azurestaticapps.net/pokedex-angular/assets/images/pokemon-green.png 404 (Not Found)
```

**Causa:** El archivo `environment.prod.ts` tenía el `imagesPath` configurado como `/pokedex-angular/assets/images`, que era la ruta utilizada para **GitHub Pages**. En Azure Static Web Apps, la aplicación se sirve desde la raíz `/`.

**Solución:** Cambiar el `imagesPath` en `environment.prod.ts`:

```typescript
// Antes (GitHub Pages)
imagesPath: '/pokedex-angular/assets/images',

// Después (Azure Static Web Apps)
imagesPath: '/assets/images',
```

---

### Error 2 — Google Fonts bloqueado por CSP

**Síntoma:**
```
Loading the stylesheet 'https://fonts.googleapis.com/css2?family=Roboto&display=swap' violates the Content Security Policy directive: "style-src 'self' 'unsafe-inline'"
```

**Causa:** La directiva `style-src` no incluía el dominio de Google Fonts, y `font-src` no incluía el dominio de los archivos de fuentes.

**Solución:** Agregar los dominios a las directivas correspondientes:

```
style-src 'self' 'unsafe-inline' https://fonts.googleapis.com;
font-src 'self' https://fonts.gstatic.com;
```

---

### Error 3 — PokéAPI bloqueada por CSP

**Síntoma:**
```
Connecting to 'https://beta.pokeapi.co/graphql/v1beta' violates the Content Security Policy directive: "default-src 'self'"
```

**Causa:** No existía una directiva `connect-src` que permitiera las llamadas a la API externa de PokéAPI (REST y GraphQL).

**Solución:** Agregar la directiva `connect-src`:

```
connect-src 'self' https://pokeapi.co https://beta.pokeapi.co;
```

---

### Error 4 — Scripts inline bloqueados por CSP

**Síntoma:**
```
Executing inline event handler violates the Content Security Policy directive 'script-src 'self''
```

**Causa:** Angular genera código inline durante el proceso de build de producción. La directiva `script-src 'self'` bloquea cualquier script que no sea un archivo externo.

**Solución:** Agregar `'unsafe-inline'` a `script-src`:

```
script-src 'self' 'unsafe-inline';
```

> **Nota:** Esto baja la calificación de A+ a A en SecurityHeaders.com, pero es necesario para el correcto funcionamiento de la aplicación Angular.

---

### Error 5 — Recursos externos bloqueados por COEP/CORP

**Síntoma:**
Las imágenes de sprites cargadas desde `https://raw.githubusercontent.com` no se mostraban.

**Causa:** Los encabezados `Cross-Origin-Embedder-Policy: require-corp` y `Cross-Origin-Resource-Policy: same-origin` bloqueaban todos los recursos de orígenes externos.

**Solución:** Eliminar ambos encabezados del `staticwebapp.config.json`, ya que la aplicación requiere cargar recursos desde múltiples orígenes externos (PokéAPI, GitHub, Google Fonts).

---

## 7. ✅ Verificación Final

### Checklist de validación

- [x] La aplicación carga correctamente en la URL de Azure.
- [x] Las imágenes de Pokémon se muestran sin errores 404.
- [x] Las fuentes de Google Fonts cargan correctamente.
- [x] Las llamadas a PokéAPI (REST y GraphQL) funcionan.
- [x] La consola del navegador no muestra errores de CSP.
- [x] La navegación SPA funciona (rutas internas y recarga de página).
- [x] SecurityHeaders.com reporta calificación **A**.

### Comandos de despliegue final

```bash
git add .
git commit -m "fix: configuración CSP y rutas para Azure Static Web Apps"
git push origin master
```

---

## 📚 Referencias

- [Documentación de Azure Static Web Apps](https://learn.microsoft.com/es-es/azure/static-web-apps/)
- [Configuración de staticwebapp.config.json](https://learn.microsoft.com/es-es/azure/static-web-apps/configuration)
- [Content Security Policy (MDN)](https://developer.mozilla.org/es/docs/Web/HTTP/CSP)
- [SecurityHeaders.com](https://securityheaders.com/)
- [PokéAPI](https://pokeapi.co/)
