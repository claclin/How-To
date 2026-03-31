# Documentación Técnica: Claudi's Knowledge Hub (CKB)

Este documento detalla la arquitectura, el funcionamiento visual y el sistema de almacenamiento interno de **Claudi's Knowledge Hub**, una aplicación monolítica (`pkb.html`) diseñada para operar 100% offline y ofrecer una experiencia de lectura de alta fidelidad.

---

## 1. Arquitectura de la Aplicación

La aplicación ha sido construida bajo la premisa de ser **totalmente funcional sin conexión a internet** (offline-first). Está encapsulada en un único archivo (`pkb.html`) que contiene:

*   **HTML Estructural**: La maquetación de la interfaz mediante un diseño lateral (Sidebar) y un área principal (Viewer).
*   **CSS "Vanilla" Avanzado**: No se depende de frameworks pesados en tiempo real. Todo el motor visual (temas de color, variables, inmersión de fondo) está definido mediante CSS nativo.
*   **Lógica JavaScript**: Maneja la interacción del DOM, el motor de parseo de Markdown a HTML (usando las librerías CDN de *marked.js* y *DOMPurify* para seguridad) y la base de datos interna.

### 1.1 Sistema de Temáticas Dinámico (Inmersión Visual)

Una de las características más destacadas de la interfaz es su sistema de **Acento Global Dinámico**.
*   Cada categoría (OSCC, CMS, General, etc.) tiene un color "hexadecimal" asignado en el array `CATEGORY_COLORS`.
*   Al seleccionar un manual, JavaScript convierte este color hexadecimal en valores transparentes (`RGBA`) mediante una función propia (`hexToRGBA`).
*   Se sobrescriben las variables CSS de la base de forma dinámica en toda la aplicación (`--accent-primary`, `--theme-bg-main`, `--theme-bg-tint`).
*   **Resultado**: La interfaz completa se "baña" en un 10% de opacidad del color de la temática seleccionada (tanto a la izquierda como a la derecha), retornando a una paleta base purista (modo Claro, Oscuro o Neutro) si se despinta el manual.

---

## 2. Motor de Almacenamiento: IndexedDB

A diferencia de las clásicas "cookies" o el "LocalStorage" (que solo admite cadenas de texto muy pequeñas y bloquea el uso de la web mientras lee o escribe), la aplicación utiliza **IndexedDB**. 

### 2.1 ¿Qué es y cómo funciona?

IndexedDB es un sistema gestor de bases de datos integrado directamente en el navegador de tu ordenador. 
*   **NoSQL / Transaccional**: La información se guarda en formato JSON u Objetos en bruto (archivos enteros, blobs, grandes bloques de texto), en lugar de usar tablas sql.
*   **Asíncrono**: Leer y escribir no bloquea la interfaz de usuario.
*   **Almacenamiento Local**: Todo reside físicamente en tu ordenador local, en una carpeta cifrada administrada por tu navegador (Chrome, Firefox, Edge, etc). No viaja a ningún servidor en internet.

En nuestra aplicación, creamos una base de datos principal llamada `CKB_Library_v4` que contiene una "tabla" (Object Store) llamada `manuals`. Cada manual importado se guarda directamente aquí íntegramente.

### 2.2 Rezagos de Versiones Anteriores

Durante el desarrollo es muy común instanciar la base de datos con distintos nombres (`ClaudiHubDB`, `ZenKnowledgeDB`, etc). 
A diferencia de los archivos en tu disco duro tradicional, **tu navegador no elimina estas bases de datos antiguas de forma automática**. Actúa bajo un principio de prudencia: no asume que un cambio de nombre en el código signifique que la base antigua es irrelevante. En consecuencia, ambas conviven.

---

## 3. Guía del Desarrollador (Gestión mediante F12)

Para poder visualizar físicamente dónde están los datos o para purgar desarrollos arcaicos, las herramientas de desarrollo del navegador son la vía indicada.

### Pasos para investigar y borrar las BBDD (Chrome, Edge, Brave):

1. **Abrir Consola F12**: Con la pestaña de la aplicación web abierta (`pkb.html`), presiona **`F12`** en tu teclado (o haz clic derecho y pulsa "Inspeccionar").
2. **Navegar a "Storage" o "Application"**: 
    *   En la barra superior de esta nueva ventana donde ves cosas como "Elements", "Console" y "Network", busca la pestaña que dice **"Application"** (o "Storage" en Firefox). Si no cabe en pantalla, usa las flechas de `>>` para ver más pestañas.
3. **Explorar el IndexedDB**:
    *   En el panel estrecho de la izquierda, baja hasta la sección "Storage" > **"IndexedDB"**.
    *   Al desplegar esa pestaña verás todas las bases de datos registradas temporalmente bajo la misma URL/IP de origen (en este caso el *localhost* o donde tengas abierto el fichero).
4. **Ver los Registros de Datos**:
    *   Si despliegas nuestra DB activa (`CKB_Library_v4`) y haces clic sobre el Object Store `manuals`, podrás ver en medio de la pantalla una tabla virtual de filas. Cada fila es uno de tus manuales guardados. ¡Puedes incluso leer su contenido ahí mismo!

### 3.1 Eliminación y Limpieza General ("Clear site data")

Si tienes un montón de bases de datos de versiones antiguas que no quieres:
*   Para borrarlas **una a una**: Selecciona el nombre superior de la base de datos (por ejemplo, "ClaudiHubDB") en el panel izquierdo y, en el gran recuadro central, busca y pulsa el botón **"Delete database"**.
*   Para un **vaciado absoluto (Agresivo)**: Dentro del mismo panel izquierdo de la pestaña de *Application*, sube de nuevo a la opción **"Storage"** que tiene un pequeño icono de un disco duro o papelera. Asegúrate de que las opciones estén marcadas (particularmente *IndexedDB*) y clica en el gran botón **"Clear site data"**.

Esto reseteará la aplicación como si acabase de "nacer", forzando que se regenere con un estado limpio, ideal cuando estás iterando nuevas funcionalidades.
