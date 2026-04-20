# 🚀 Despliegue de la Aplicación Pokédex en Azure Static Web Apps

> Documentación del proceso de despliegue de una aplicación Angular en **Azure Static Web Apps**,
> incluyendo configuraciones, errores encontrados y soluciones aplicadas.

🔗 Repositorio: [github.com/SergioOlier/pokedex](https://github.com/SergioOlier/pokedex)

---

## 🛠️ Tecnologías utilizadas

- **Angular** — Framework frontend
- **Node.js**
- **GitHub** — Repositorio y CI/CD
- **Azure Static Web Apps**

---

## 📦 1. Construcción del Proyecto

Para generar la versión de producción se utilizó:

```bash
npm install
npm run build
```

Esto genera los archivos optimizados en la carpeta:
/dist/

---

## ☁️ 2. Creación del Recurso en Azure

Se creó la aplicación en **Azure Static Web Apps** con la siguiente configuración:

| Parámetro | Valor |
|---|---|
| Nombre | `pokedex-Kanto` |
| Plan | Free |
| Región | east2 |
| Suscripción | Azure for Students |

---

## 🔗 3. Integración con GitHub

Se conectó el repositorio público: [github.com/SergioOlier/pokedex](https://github.com/SergioOlier/pokedex)

| Parámetro | Valor |
|---|---|
| Rama | `main` |
| Build preset | Angular |
| App location | `/` |
| Output location | `dist` |

> Azure generó automáticamente un pipeline de despliegue mediante GitHub Actions.

---

## ⚙️ 4. Pipeline de Despliegue (GitHub Actions)

Se generó automáticamente el archivo:
.github/workflows/azure-static-web-apps.yml

Este workflow permite:

- Construir la aplicación Angular
- Desplegar automáticamente en Azure
- Ejecutarse en cada `push` a la rama `main`

---

## 🌐 5. Despliegue Automático

Para desplegar la aplicación se ejecutaron los siguientes comandos:

```bash
git add .
git commit -m "pokedex website"
git push origin main
```

> Esto activó el pipeline de Azure, realizando el despliegue automático.

---

## ⚠️ Problemas Encontrados y Soluciones

### ❌ Problema 1: Imágenes de Pokémon no cargaban

**Descripción:** Al desplegar en Azure, las imágenes no se mostraban correctamente.

**Causa:** Ruta incorrecta en el archivo de entorno de producción:

```ts
imagesPath: '/pokedex-angular/assets/images'
```

**Solución:** Se modificó la ruta en `/src/environments/environment.prod.ts`:

```ts
imagesPath: '/assets/images'
```

✅ **Resultado:** Las imágenes se cargaron correctamente en producción.

---

### ❌ Problema 2: Baja calificación en Security Headers

**Descripción:** El sitio obtenía calificación `A` en seguridad en lugar de `A+`.

**Causa:** Faltaba el encabezado `Permissions-Policy`.

**Solución:** Se agregó en `staticwebapp.config.json`:

```json
"Permissions-Policy": "camera=(), microphone=(), geolocation=(), fullscreen=(self)"
```

✅ **Resultado:** Calificación mejorada a `A+`.

---

### ❌ Problema 3: Uso inseguro de `unsafe-inline` en CSP

**Descripción:** Advertencia detectada en Content Security Policy:
This policy contains 'unsafe-inline' which is dangerous in the script-src directive.

**Riesgo:** Permite la ejecución de scripts inline, facilitando ataques XSS.

**Configuración inicial:**
"script-src 'self' 'unsafe-inline'"

**Solución:** Se eliminó `'unsafe-inline'` de la directiva `script-src`:

```json
"Content-Security-Policy": "default-src 'self'; img-src 'self' data: https:; script-src 'self'; style-src 'self' 'unsafe-inline' https:; font-src 'self' https: data:; connect-src 'self' https:;"
```

✅ **Resultado:** Advertencia eliminada y seguridad mejorada.

---

## 🔐 6. Configuración de Seguridad

Se configuraron los siguientes encabezados HTTP en `staticwebapp.config.json`:

- `Content-Security-Policy` (CSP)
- `Strict-Transport-Security` (HSTS)
- `X-Content-Type-Options`
- `X-Frame-Options`
- `Referrer-Policy`
- `Permissions-Policy`

---

## 🧪 7. Validación de Seguridad

### 🔍 Análisis con SSL Labs

| Parámetro | Resultado |
|---|---|
| Calificación | `A+` |
| TLS 1.3 | ✅ Soportado |
| Certificado | ✅ Válido |
| HSTS | ✅ Habilitado |

---

## 📈 Resultado Final

- ✅ Aplicación desplegada correctamente en Azure
- ✅ HTTPS activo por defecto
- ✅ Encabezados de seguridad correctamente configurados
- ✅ Calificación `A+` en seguridad
- ✅ Aplicación funcional sin errores en producción
