# Control de ventas — acceso por correo con Firebase

La app es un sitio estático (`index.html`). El inicio de sesión y los permisos los maneja
**Firebase** (Authentication con enlace por correo + reglas de Firestore). Una página sola no
puede proteger nada; las reglas de Firestore son las que impiden leer o escribir.

## Cómo funciona el acceso
- Se entra con un **enlace enviado al correo** (sin contraseñas).
- Solo los correos de la colección `users` ven datos. Tu correo (`naramburo.cm@gmail.com`) es el único **administrador**.
- El administrador carga/modifica datos y da o quita acceso desde *Datos → Acceso*.
- Los demás son **lectores**: ven el resumen; Firestore les impide escribir.
- Cualquiera puede pedir un enlace, pero si su correo no está en `users` no ve nada.

## Pasos
1. https://console.firebase.google.com → **Agregar proyecto** (el plan gratuito Spark sirve).
2. **Build → Authentication → Comenzar → Método de acceso → Correo electrónico/contraseña**:
   activa el proveedor y también **"Vínculo del correo electrónico (acceso sin contraseña)"**.
3. **Authentication → Configuración → Dominios autorizados**: agrega el dominio donde publicarás
   (por ejemplo `mi-app.vercel.app`). `localhost` ya viene incluido.
4. **Build → Firestore Database → Crear base de datos** (modo producción, la región que prefieras).
5. **Firestore → Reglas**: pega el contenido de `firestore.rules` y **Publicar**.
6. **Firestore → Datos → Iniciar colección** `users` → ID del documento: `naramburo.cm@gmail.com`
   (en minúsculas) → campo `role` (string) = `admin`.
7. **Configuración del proyecto → Tus apps → Web (</>)**: registra la app y copia `firebaseConfig`
   dentro de `FIREBASE_CONFIG` en el archivo **`config.js`** (apiKey, authDomain, projectId, appId). Es un solo lugar para las dos pantallas.
   Esos valores son públicos por diseño; la protección son las reglas.
8. **Publica la carpeta**: en Vercel (arrastrarla en vercel.com/new, sin build) o con
   `firebase deploy` (Firebase Hosting). Agrega ese dominio en el paso 3.
9. Abre el sitio, escribe tu correo, abre el enlace que llega (en el mismo dispositivo y navegador).
   La primera vez la app crea los datos iniciales: **vuelve a subir tu plantilla de metas y tus
   archivos de venta diaria** en *Datos*.

## Notas
- Si abres el enlace en otro dispositivo, la app te pide escribir el correo para confirmarlo.
- Para dar acceso a alguien: *Datos → Acceso → Agregar correo* (en minúsculas). Esa persona pide
  su enlace desde el sitio con ese mismo correo.
- Firebase limita cuántos enlaces por correo se envían al día; es suficiente para un equipo pequeño.

## Terminal para vendedores (`terminal.html`)
- Pantalla de solo lectura: el vendedor escribe **"Ruta 01"** (o "1") y ve su ficha del mes, la de la semana,
  su promedio diario actual, el objetivo diario requerido, su venta por día y su venta por producto
  (con el selector Mes / Semana). No tiene pestaña de datos.
- Los datos los carga y configura el administrador desde `index.html`; la terminal los lee en vivo.
- Para dar acceso: *Datos → Acceso → Tipo de acceso → Vendedor (solo terminal de ruta)*.
  Quien entra a `index.html` con ese tipo de acceso es enviado a la terminal.
- El enlace de la terminal es `https://TU-SITIO/terminal.html`.
- Aviso: el vendedor puede escribir cualquier ruta, no solo la suya, y las reglas de Firestore le permiten
  leer los datos de la app. Si necesitas que cada vendedor vea únicamente su ruta, hay que separar los
  datos por ruta y asignarle una ruta a cada usuario (se puede agregar después).
