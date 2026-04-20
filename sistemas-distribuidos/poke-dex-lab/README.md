# 🔴 Pokédex Angular — Despliegue en Azure Static Web Apps

## 📋 Descripción del Proyecto

Aplicación web tipo Pokédex desarrollada con [Angular](https://angular.io/) que consume la API RESTful [PokéAPI](https://pokeapi.co/). Desplegada en **Azure Static Web Apps** con encabezados de seguridad configurados.

🌐 **URL en producción:** [https://jolly-dune-0a5bacc0f.7.azurestaticapps.net](https://jolly-dune-0a5bacc0f.7.azurestaticapps.net)

---

## ☁️ Creación de la Cuenta en Azure

### Paso 1 — Acceder al portal de Azure

1. Ingresar a [https://azure.microsoft.com/es-es/free/students/](https://azure.microsoft.com/es-es/free/students/).
2. Hacer clic en **"Comenzar gratis"**.

### Paso 2 — Iniciar sesión con cuenta institucional

1. Utilizar el correo electrónico institucional (cuenta `.edu`) para autenticarse.
2. Completar la verificación de identidad con el método solicitado (teléfono o correo alternativo).

### Paso 3 — Activar la suscripción Azure for Students

1. Una vez autenticado, Azure redirige al formulario de activación de **Azure for Students**.
2. Verificar que los datos del perfil (nombre, país, institución) sean correctos.
3. Aceptar los términos y condiciones del servicio.
4. Hacer clic en **"Activar"** para obtener los **$100 USD de crédito gratuito** sin necesidad de tarjeta de crédito.

### Paso 4 — Verificar la suscripción

1. Ir a [https://portal.azure.com](https://portal.azure.com).
2. Navegar a **Suscripciones** en el menú lateral.
3. Confirmar que aparece la suscripción **"Azure for Students"** con estado **Activa**.

---

## 🛠️ Requisitos Mínimos

| Herramienta | Enlace |
|---|---|
| Node.js (v18+) | [Descargar](https://nodejs.org/en/download/) |
| Angular CLI | [Guía de instalación](https://angular.io/guide/setup-local) |
| Git | [Descargar](https://git-scm.com/downloads) |
| Visual Studio Code | [Descargar](https://code.visualstudio.com/download) |
| Cuenta de Azure | [Azure for Students](https://azure.microsoft.com/es-es/free/students/) |

---

## 🚀 Instalar y Ejecutar Localmente

```bash
# 1. Clonar el repositorio
git clone <url-del-repositorio>

# 2. Navegar al directorio del proyecto
cd sistemas-distribuidos/poke-dex-lab/source/pokedex-angular/

# 3. Instalar dependencias
npm install

# 4. Ejecutar en modo desarrollo
npm start
```

5. Abrir el navegador en [http://localhost:4200/](http://localhost:4200/).

---

## 📦 Compilar para Producción

```bash
npm run build
```

La carpeta de salida se genera en `dist/pokedex-angular/` y contiene todos los archivos listos para despliegue.

---

## 🔒 Encabezados de Seguridad Implementados

Los encabezados se configuran en el archivo `staticwebapp.config.json`:

| Encabezado | Valor | Propósito |
|---|---|---|
| `Content-Security-Policy` | Directivas restrictivas por recurso | Previene XSS, inyección de código y carga de recursos no autorizados |
| `Strict-Transport-Security` | `max-age=31536000; includeSubDomains; preload` | Fuerza HTTPS en todas las conexiones |
| `X-Content-Type-Options` | `nosniff` | Evita MIME-type sniffing |
| `X-Frame-Options` | `SAMEORIGIN` | Previene ataques de clickjacking |
| `Referrer-Policy` | `strict-origin-when-cross-origin` | Controla información enviada en el header Referer |
| `Permissions-Policy` | Deshabilita cámara, micrófono, geolocalización | Restringe acceso a APIs del navegador |
| `X-XSS-Protection` | `1; mode=block` | Capa adicional contra XSS en navegadores legacy |
| `Cross-Origin-Opener-Policy` | `same-origin` | Aísla el contexto de navegación |

---

## 📚 Referencias

- [Keiler Mora — Autor original](https://github.com/keilermora)
- [PokéAPI](https://pokeapi.co/)
- [Angular](https://angular.io/)
- [Azure Static Web Apps](https://learn.microsoft.com/es-es/azure/static-web-apps/)