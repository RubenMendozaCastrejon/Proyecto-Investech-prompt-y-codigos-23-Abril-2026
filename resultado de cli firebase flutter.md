¡Excelente elección! Integrar **Firebase** en **Flutter** es el paso definitivo para darle superpoderes a tu app (base de datos en tiempo real, autenticación, notificaciones, etc.).

Aquí tienes la guía completa, estructurada paso a paso, para configurar tu entorno en Windows desde cero.

---

## 1. Instalación de Node.js y npm (Paso previo)

Para usar las herramientas de Firebase, primero necesitas **Node.js**, que incluye automáticamente **npm** (el gestor de paquetes que descargará Firebase por ti).

### Software Necesario
* **Instalador oficial:** Descarga la versión **LTS** (Long Term Support) desde [nodejs.org](https://nodejs.org/). Es la más estable para desarrollo.
* **Sistema Operativo:** Windows 10 u 11.

### Procedimiento: Cómo verificar si ya lo tienes
Abre una terminal (PowerShell o CMD) y escribe:
```bash
node -v
npm -v
```
* **Si aparecen números de versión** (ej. `v20.12.0`), ya lo tienes.
* **Si dice "no se reconoce como un comando"**, sigue los pasos de abajo.

### Paso a paso para instalar Node.js y npm (Global)
1.  **Ejecuta el instalador:** Haz doble clic en el archivo `.msi` descargado.
2.  **Acepta todo:** Haz clic en *Next* (Siguiente) en todas las ventanas. 
3.  **IMPORTANTE:** Asegúrate de que la casilla **"Add to PATH"** esté marcada (lo está por defecto). Esto es lo que permite que el comando funcione "globalmente" en cualquier carpeta.
4.  **Tools for Native Modules:** Si te pregunta si quieres instalar herramientas para módulos nativos (Chocolatey), puedes decirle que sí o saltarlo; para Firebase CLI no es estrictamente obligatorio, pero ayuda a futuro.
5.  **Reinicia la terminal:** Una vez finalizado, cierra y vuelve a abrir tu terminal para que reconozca los cambios.

---

## 2. Instalación de Firebase CLI (firebase-tools)

Una vez que `npm` funciona, instalaremos las herramientas de Firebase de forma **global** para que puedas usarlas en cualquier proyecto de Flutter.

### Comando de Instalación Global
Ejecuta el siguiente comando en tu terminal:
```bash
npm install -g firebase-tools
```
> El flag `-g` significa "global", lo que permite que el comando `firebase` esté disponible en todo tu sistema.

### Cómo verificar la instalación
```bash
firebase --version
```

---

## 3. Comandos Esenciales de Firebase

### Cómo acceder a Firebase con tu Cuenta de Google
Para que la consola sepa quién eres y a qué proyectos tienes acceso, debes iniciar sesión:

1.  En la terminal, escribe:
    ```bash
    firebase login
    ```
2.  Se abrirá automáticamente una pestaña en tu **navegador web**.
3.  Selecciona tu cuenta de Google y otorga los permisos necesarios.
4.  Regresa a la terminal; verás un mensaje de éxito: *"Success! Logged in as [tu-correo]@gmail.com"*.

### Cómo usar `firebase-tools` en Flutter
Aunque puedes hacer mucho desde la terminal de Firebase, para Flutter se utiliza un "asistente" adicional llamado **FlutterFire CLI**.

1.  **Instala el asistente de Flutter:**
    ```bash
    dart pub global activate flutterfire_cli
    ```
2.  **Configura tu proyecto:**
    Entra a la carpeta raíz de tu aplicación Flutter y ejecuta:
    ```bash
    flutterfire configure
    ```
    Este comando (que usa las herramientas de Firebase que instalamos antes) te permitirá seleccionar tus proyectos de la consola y creará automáticamente el archivo `firebase_options.dart` con todas las credenciales.

---

### Resumen de comandos útiles:

| Acción | Comando |
| :--- | :--- |
| **Login** | `firebase login` |
| **Cerrar sesión** | `firebase logout` |
| **Listar proyectos** | `firebase projects:list` |
| **Ayuda general** | `firebase --help` |
| **Inicializar servicios** | `firebase init` |
