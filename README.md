# Claudi's Knowledge Hub

**Claudi's Knowledge Hub** es una aplicación monolítica (*offline-first*) diseñada para gestionar, organizar y leer documentación técnica y manuales personales en formato Markdown de manera inmersiva, rápida y 100% privada.

## Características Principales

*   **100% Offline y Privado:** Toda tu base de conocimientos vive en una única página HTML (`pkb.html`) que almacena la información directamente en tu PC utilizando la base de datos nativa de tu explorador (**IndexedDB**). Nada se sube a internet.
*   **Acento Global Dinámico:** La interfaz visual se adapta contextualmente. Al seleccionar un manual sobre una tecnología específica (OSCC, CMS, n8n, General), el sistema colorea dinámicamente tanto la barra lateral como el panel de lectura para brindarte una inmersión atmosférica (10% de opacidad) que facilita saber continuamente en qué bloque estás trabajando.
*   **Renderizado Robusto:** Carga archivos Markdown sin esfuerzo. Las cabeceras principales (H1) seccionan e indexan la tabla de contenidos, permitiendo navegación instantánea a bloques gigantes de texto. Incorpora de paso librerías externas mediante CDN como `marked.js` para parseo y `DOMPurify` para sanitización.
*   **Temas Claros, Oscuros y Neutros:** La experiencia de lectura es cuidada sin depender de `Tailwind` u otros frameworks externos (Vanilla CSS). Las transiciones inmersivas cambian basándose en tu perfil de color guardado en `LocalStorage`.
*   **UI Dinámica:** Integración optimizada de íconos mediante Lucide.

## Arquitectura

- **`pkb.html`**: Monolito principal (HTML / JS / CSS). Contiene todo el motor de lógica (Lectura IndexedDB, parseo, visualización).
- **`Documentacion_CKB.md`**: Detalles técnicos internos sobre la estructura base y guía de depuración local.
- **`GEMINI.md` / `.agents/`**: Configuración de reglas y flujos de trabajo en el Workspace de Deepmind Antigravity.

## Gestión y Uso

Para usarlo solo necesitas abrir `pkb.html` en Chrome, Edge o Firefox. 
Para gestionar y limpiar la base de datos interna, puedes abrir las Herramientas de Desarrollador del navegador (F12) e inspeccionar el apartado `Application > Storage > IndexedDB`.
