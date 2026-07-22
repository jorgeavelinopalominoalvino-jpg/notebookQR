# Instrucciones — Notebook QR con panel docente

Esta guía te lleva desde cero hasta tener el sitio funcionando con Firebase. No necesitás instalar nada para las partes 1 a 4; recién en la parte 6 (publicar) vas a elegir una de dos opciones.

## Qué cambió respecto a tu versión anterior

- `index.html`: el sitio que ven los alumnos. Antes las materias y temas estaban escritos a mano en el HTML; ahora se cargan en vivo desde una base de datos (Firestore).
- `admin.html`: **nuevo**. Panel privado para que los docentes agreguen, editen o eliminen materias y temas sin tocar código.
- `firestore.rules`: reglas de seguridad. Cualquiera puede leer el contenido; solo un docente logueado puede escribir.
- Las secciones de **Tareas** y **Foro de dudas** siguen funcionando igual que antes (guardadas en el navegador de cada alumno).

---

## 1. Creá tu proyecto de Firebase

1. Entrá a [console.firebase.google.com](https://console.firebase.google.com) con una cuenta de Google.
2. Clic en **Crear un proyecto**, ponele un nombre (ej: `notebook-qr`) y seguí los pasos (podés desactivar Google Analytics, no hace falta).

## 2. Registrá una app web y copiá tu configuración

1. Dentro del proyecto, clic en el ícono **`</>`** ("Agregar app" → Web).
2. Ponele un apodo (ej: `notebook-qr-web`) y clic en **Registrar app**. No hace falta Firebase Hosting en este paso.
3. Firebase te va a mostrar un bloque `firebaseConfig` parecido a este:

```js
const firebaseConfig = {
  apiKey: "AIzaSy...",
  authDomain: "notebook-qr.firebaseapp.com",
  projectId: "notebook-qr",
  storageBucket: "notebook-qr.appspot.com",
  messagingSenderId: "123456789",
  appId: "1:123456789:web:abc123"
};
```

4. Copiá esos valores y pegalos **en dos archivos**: `index.html` y `admin.html`. En ambos vas a encontrar (con Ctrl+F) el texto `PON_AQUI_TU_API_KEY` — reemplazá todo el objeto `firebaseConfig` por el tuyo, en los dos archivos por igual.

## 3. Activá Firestore Database

1. En el menú lateral: **Compilación → Firestore Database → Crear base de datos**.
2. Elegí una ubicación (cualquiera de Sudamérica está bien) y arrancá en **modo producción**.
3. Andá a la pestaña **Reglas** y reemplazá todo el contenido por lo que está en el archivo `firestore.rules` de esta carpeta. Clic en **Publicar**.

Esto hace que cualquiera pueda ver el contenido del sitio, pero solo alguien logueado pueda agregar o borrar materias/temas.

## 4. Activá Authentication y creá tu(s) usuario(s) docente

1. En el menú lateral: **Compilación → Authentication → Comenzar**.
2. En la pestaña **Sign-in method**, activá el proveedor **Correo electrónico/contraseña**.
3. Andá a la pestaña **Users** → **Add user** y creá una cuenta para cada docente (correo + contraseña). Estas son las cuentas con las que van a entrar a `admin.html`.

> No hay un formulario público de registro a propósito: así solo entra al panel quien vos autorices creando su usuario acá.

## 5. Confirmá que la configuración quedó bien

Abrí `index.html` haciendo doble clic (se abre en el navegador). Deberías ver el diseño normal, pero en la sección de materias va a decir "Todavía no hay materias cargadas" — es esperado, la base de datos está vacía todavía.

## 6. Publicá el sitio

Los archivos usan `<script type="module">` para conectarse a Firebase, lo que requiere que el sitio esté **publicado en una dirección web** (no alcanza con abrir el archivo desde tu compu para que el login funcione). Elegí una opción:

### Opción A — Firebase Hosting (recomendada, gratis)

1. Instalá Node.js si no lo tenés ([nodejs.org](https://nodejs.org)).
2. Abrí una terminal en la carpeta con tus archivos y ejecutá:
   ```
   npm install -g firebase-tools
   firebase login
   firebase init hosting
   ```
   Cuando pregunte por la carpeta pública, indicá la carpeta donde están `index.html` y `admin.html` (o `.` si están en la misma carpeta). Cuando pregunte si es una single-page app, elegí **No**.
3. Publicá con:
   ```
   firebase deploy
   ```
4. Te va a dar una URL tipo `https://notebook-qr.web.app` — esa es la dirección final de tu sitio.

### Opción B — GitHub Pages (sin instalar nada)

1. Creá un repositorio nuevo en [github.com](https://github.com) y subí `index.html`, `admin.html` y este mismo archivo.
2. En el repositorio: **Settings → Pages → Deploy from a branch**, elegí la rama `main` y guardá.
3. GitHub te da una URL tipo `https://tu-usuario.github.io/tu-repositorio/`.

Con cualquiera de las dos opciones, entrá luego a **Authentication → Settings → Authorized domains** en Firebase y confirmá que el dominio que te dieron (`...web.app` o `...github.io`) esté en la lista (Firebase Hosting lo agrega solo; GitHub Pages puede necesitar que lo agregues a mano).

## 7. Usá el panel docente

1. Entrá a `admin.html` (o al botón "🔑 Docentes" del sitio) e iniciá sesión con un usuario que hayas creado en el paso 4.
2. Si es la primera vez, tocá **"Cargar contenido de ejemplo"** para recrear las materias y temas originales (Inglés, CCSS, Matemáticas) y tener algo para mostrar de entrada.
3. Para agregar contenido real de una clase: completá **"Agregar materia"** una vez por materia, y **"Agregar tema"** por cada clase (con el video o imagen correspondiente).
4. Para las imágenes/videos: subilos a algún servicio con enlace público (Google Drive con "cualquiera con el enlace", YouTube, Imgur, etc.) y pegá ese link en el campo "URL del recurso".
5. Desde "Contenido actual" podés editar o eliminar cualquier tema o materia ya cargada.

---

## Estructura de datos (por si necesitás explicarla en la sustentación)

```
materias (colección)
 └─ {materiaId} (documento)
      nombre, emoji, subtitulo, imagen
      └─ temas (subcolección)
           └─ {temaId} (documento)
                titulo, descripcion, tipoRecurso ("imagen"|"video"),
                urlRecurso, etiquetas (lista), creadoEn (fecha)
```

## Problemas comunes

- **"Todavía no hay materias cargadas"** en el sitio público → normal si la base está vacía; entrá al panel docente y cargá contenido (o usá el botón de contenido de ejemplo).
- **No puedo iniciar sesión en `admin.html`** → confirmá que el usuario existe en Authentication → Users, y que activaste el proveedor Correo electrónico/contraseña.
- **El sitio no carga nada y no tira error visible** → revisá que copiaste el mismo `firebaseConfig` completo en `index.html` Y en `admin.html`.
- **Funciona en `admin.html` pero no puedo guardar nada** → revisá que las reglas de Firestore estén publicadas tal cual las de `firestore.rules`, y que iniciaste sesión (el panel debe mostrar tu correo arriba).
