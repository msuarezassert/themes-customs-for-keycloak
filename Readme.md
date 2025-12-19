# 🎨 Keycloak Themes – Guía de Creación y Desarrollo

Este documento describe cómo descargar los themes base de Keycloak, entender su estructura y ejecutar Keycloak en Docker para desarrollar themes de forma iterativa.

## 📦 Descarga de los themes base de Keycloak

Keycloak provee un JAR con los themes por defecto que sirve como referencia para crear themes personalizados.

### Comando Docker para descargar el JAR

```bash
docker run --rm \
  -v $(pwd):/workspace \
  quay.io/keycloak/keycloak:{project_versionMvn} \
  sh -c "cp /opt/keycloak/lib/lib/main/org.keycloak.keycloak-themes-{project_versionMvn}.jar /workspace/"
```

Esto permitirá explorar los themes oficiales y usarlos como base.

## 📁 Estructura de carpetas de un Theme

Un theme de Keycloak vive dentro de:

```
themes/
└── my-theme/
    ├── login/
    ├── account/
    ├── admin/
    ├── email/
    ├── resources/
    │   ├── css/
    │   ├── js/
    │   └── img/
    ├── theme.properties
```

### 🔹 Carpetas principales

| Carpeta   | Descripción                          |
|-----------|--------------------------------------|
| login     | Pantallas de login, registro, reset de contraseña |
| account   | Consola de usuario final             |
| admin     | Consola de administración            |
| email     | Templates de emails                  |
| resources | Recursos estáticos (CSS, JS, imágenes) |

### ⚙️ theme.properties

Archivo central de configuración del theme.

**Ejemplo:**

```properties
parent=keycloak
import=common/keycloak

styles=css/login.css
scripts=js/custom.js
```

**Propiedades importantes:**

- `parent`: theme base del cual hereda (ej: keycloak, base)
- `import`: reutiliza recursos comunes
- `styles`: CSS custom
- `scripts`: JS custom

💡 Siempre conviene heredar de un theme base para minimizar mantenimiento.

## 🎨 PatternFly y React en Keycloak

Keycloak utiliza PatternFly como sistema de diseño oficial, tanto para las pantallas de login como para las consolas modernas.

### 🧩 PatternFly

PatternFly es un design system basado en:

- CSS
- Variables
- Componentes visuales

Provee:

- Layouts
- Tipografías
- Colores
- Componentes (botones, formularios, alerts)

En los themes de Keycloak:

- El HTML se genera con FreeMarker (.ftl)
- Las clases CSS corresponden a PatternFly
- La personalización se realiza principalmente vía:
  - Override de CSS
  - Variables
  - Assets (logos, imágenes)

📌 Recomendación: respetar las clases de PatternFly y sobrescribir estilos en lugar de reescribir layouts.

### ⚛️ React (Account & Admin Console)

Desde Keycloak 17+, las consolas:

- Account
- Admin

Están desarrolladas en React + PatternFly

**Implicancias para los themes:**

- No se editan templates .ftl
- El theming se limita a:
  - CSS overrides
  - Branding (logos, colores)
- Cambios profundos requieren:
  - Build del frontend (fuera del scope de themes clásicos)

## 🐳 Ejecutar Keycloak con Docker y Bind Mounts

Vamos a ejecutar Keycloak montando:

- 📂 data → persistencia de datos
- 🎨 themes → desarrollo en caliente de themes

### 📁 Estructura recomendada

```
keycloak-customizations/
├── data/
└── themes/
    └── my-theme/
```

### ▶️ Comando Docker

```bash
docker run -d \
  --name keycloak \
  -p 8080:8080 \
  -v $(pwd)/data:/opt/keycloak/data \
  -v $(pwd)/themes:/opt/keycloak/themes \
  -e KEYCLOAK_ADMIN=admin \
  -e KEYCLOAK_ADMIN_PASSWORD=admin \
  quay.io/keycloak/keycloak:{project_versionMvn} \
  start-dev
```

**📌 Acceso:**

- URL: http://localhost:8080
- Usuario: admin
- Password: admin

### 🔄 Desarrollo en caliente

Cambios en:

- .ftl
- CSS
- JS
- imágenes

Se reflejan al refrescar el navegador

A veces es necesario:

- Hard refresh
- Limpiar cache
- Reiniciar el contenedor

## ✅ Recomendaciones finales

- Copiar un theme base y modificarlo
- No tocar themes oficiales directamente
- Versionar los themes en Git
- Mantener overrides pequeños y controlados
- Usar PatternFly como guía visual