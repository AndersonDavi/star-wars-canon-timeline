# Orden del Canon · Star Wars

Guía interactiva para ver todo el canon audiovisual de Star Wars (películas y series) en una sola línea de visionado: en orden **cronológico** de la historia o en orden de **estreno**.

**Demo:** https://andersondavi.github.io/star-wars-canon-timeline/

## Qué tiene

- 75 entradas numeradas, con The Clone Wars separado en sus tramos oficiales y cada capítulo marcable por separado.
- Progreso con barra de sable de luz, conteo por época, botón **Seguir viendo** y búsqueda por título o capítulo.
- Filtros Todos / Películas / Series.
- El progreso se guarda en el navegador. Se puede descargar o cargar como JSON y, si quieres, **sincronizar con tu Google Drive** para seguir desde otro equipo o el celular.
- Se puede instalar como app y funciona sin conexión.

## Cómo correrla

No hay compilación ni dependencias: son archivos estáticos.

- Abre `index.html` en el navegador, o
- sirve la carpeta: `python -m http.server 8788` y entra a http://localhost:8788 (necesario para Drive y para el modo sin conexión).

GitHub Pages publica la rama `main` tal cual.

## Sincronización con Google Drive

No usa servidor. El navegador pide un permiso de Google con el alcance `drive.file` (solo da acceso a los archivos que crea la guía) y guarda `star-wars-progreso.json` en la carpeta *Guía Star Wars* del Drive de cada persona. Al sincronizar, el progreso de varios equipos se combina carta por carta: gana el cambio más reciente.

Para activarla en otro despliegue:

1. En [Google Cloud](https://console.cloud.google.com/) crea un proyecto y habilita la **Google Drive API**.
2. En **Google Auth Platform** completa la marca (nombre, correo de soporte, página principal y la URL de `privacidad.html`), elige público **Externo**, agrega el permiso `.../auth/drive.file` y publica la app.
3. En **Clientes** crea uno de tipo **Aplicación web** con tu dominio (y `http://localhost:8788`) en *Orígenes autorizados de JavaScript*. No hace falta URI de redirección ni el secreto del cliente.
4. Pon el ID de cliente en `DRIVE_CLIENT_ID` y tu dominio en `DRIVE_HOSTS`, dentro de `index.html`.

## Estructura

| Archivo | Qué es |
|---|---|
| `index.html` | Toda la app: estilos, datos (`ERAS`) y script |
| `privacidad.html` | Política de privacidad (la pide Google para Drive) |
| `sw.js`, `manifest.webmanifest`, `icons/` | App instalable y sin conexión |
| `posters/` | Portadas en WebP |
| `CHANGELOG.md` | Historial de versiones |

## Créditos

Portadas oficiales cortesía de [TMDB](https://www.themoviedb.org/). Orden contrastado con las guías publicadas por Lucasfilm en starwars.com. Íconos de Boba Fett y de la Alianza Rebelde de [SVG Repo](https://www.svgrepo.com/).

Guía hecha por fans, sin relación con Lucasfilm ni Disney. Star Wars y sus logotipos son marcas de Lucasfilm Ltd.
