# Changelog — Guía Star Wars (Orden del Canon)

Todas las versiones notables de esta guía interactiva de visionado.
Formato basado en [Keep a Changelog](https://keepachangelog.com/). Versionado [SemVer](https://semver.org/).

## [1.3.0] — 2026-10-18

### Añadido
- Botón **Seguir viendo**: lleva a la primera entrada sin marcar del orden y filtro actuales y la resalta.
- Progreso por época (`3/8`) con **Marcar todo / Desmarcar** y aviso para deshacer.
- Búsqueda por título, descripción o nombre de capítulo, sin importar tildes; si coincide un capítulo, abre la lista y lo resalta. En celular se abre con la lupa.
- Capítulos de The Clone Wars marcables uno por uno: la tarjeta muestra "3 de 15 capítulos" y una barra parcial, y queda vista al marcar el último. También se sincronizan con Drive y van en el JSON.
- App instalable y sin conexión (manifest, service worker e íconos con la insignia rebelde); "Instalar app" aparece en el menú ⋯ cuando el navegador lo permite.
- Logo de Star Wars en la barra superior, aviso de marcas en el pie y README.

### Cambiado
- Rediseño visual: el sable zumba y lo recorre un destello de energía; al marcar, un tajo de luz cruza el póster y el check rebota; las tarjetas aparecen al hacer scroll y se iluminan con el color de su época al pasar el mouse; estrellas que titilan y estrellas fugaces en el fondo; el nodo de la época se enciende al completarla.
- Portadas en WebP de 500 px: de 7,2 MB a 1,5 MB.

## [1.2.0] — 2026-10-18

### Añadido
- Sincronización opcional con Google Drive, sin servidor: el progreso se guarda en `Guía Star Wars/star-wars-progreso.json` del Drive de cada persona (permiso `drive.file`, que solo da acceso a ese archivo). Se sube sola unos segundos después de marcar algo y al salir de la página; el botón de Drive muestra el estado (En Drive, Sincronizando, Sin sincronizar, Sincronizar cuando venció el permiso de una hora).
- Combinación entre equipos: cada carta guarda cuándo se marcó o desmarcó, y al sincronizar gana el cambio más reciente de cada una. El JSON descargado usa el mismo formato (`marks`) y sigue trayendo `watched` para versiones anteriores.
- Página de privacidad (`privacidad.html`), enlazada desde el pie y desde la ventana de Drive.

### Cambiado
- Descargar/Cargar progreso (JSON) y Reiniciar pasan a un menú "⋯" en la barra superior.
- Los filtros y el orden son controles segmentados: Todos / Películas / Series y Cronológico / Estreno (el botón anterior mostraba el orden contrario al activo).
- En celular la barra superior ocupa la mitad del alto (marca y acciones en una línea, sable debajo, filtros en una sola fila) y los márgenes son más estrechos.

### Corregido
- Reiniciar el progreso ya no vuelve a ejecutar la migración de v1.0.0 al recargar.

## [1.1.0] — 2026-07-23

### Añadido
- Toggle "Orden cronológico ↔ Orden de emisión" en la barra de herramientas, en reemplazo del botón "Compactar".
- Orden de emisión: las mismas tarjetas reordenadas por fecha real de estreno (investigada y verificada por título/temporada), agrupadas en nuevas "eras de estreno" — Trilogía Original, Precuelas, The Clone Wars (película y serie), Rebels y la Trilogía Secuela, Amanecer del Mandoverso, Edad dorada de Disney+ y Era actual del streaming.
- Cada tarjeta incorpora un campo de fecha de estreno real (`rel`) usado para ordenar y agrupar la nueva vista.

### Eliminado
- Vista "Compactar" (botón, CSS y JS): sustituida por el nuevo alternador de orden, más útil para comparar cronología interna vs. orden real de lanzamiento.

### Corregido
- Los filtros Todos/Películas/Series, el guardado local, el export/import de JSON, el modal de reinicio y el despliegue de episodios funcionan igual en ambas vistas.

## [1.0.1] — 2026-07-23

### Corregido
- Orden contrastado con las guías vigentes de Lucasfilm para películas, series y los 133 episodios de The Clone Wars.
- Tales of the Underworld se divide en sus arcos: Cad Bane antes del Episodio I y Ventress antes de su encuentro con el Bad Batch en T3 E9.
- The Path of Fear queda inmediatamente después de The Clone Wars T4 E19, durante la masacre de Dathomir.
- Tales of the Empire se separa por episodios para no mezclar la juventud de Morgan Elsbeth, el arco de Barriss y la Nueva República en una sola tarjeta fuera de época.
- Maul: Shadow Lord indica los 10 episodios completos de su primera temporada.
- La nota metodológica distingue el orden cronológico confirmado de los solapamientos sin intercalación oficial.
- El progreso local y los JSON de v1.0.0 se migran a las nuevas tarjetas sin perder lo ya marcado.

## [1.0.0] — 2026-07-23

Primera versión completa.

### Añadido
- Secuencia cronológica continua del canon audiovisual (67 entradas, numeradas 01→67), en una sola línea de visionado.
- Explosión de The Clone Wars en sus tramos por saltos de orden, con los nombres de cada episodio a la vista.
- El Episodio III colocado entre los arcos de la Temporada 7 (el Sitio de Mandalore transcurre en paralelo).
- Portadas oficiales de cada película y serie (cortesía de TMDB), en blanco y negro que pasan a color al marcar.
- Línea de tiempo vertical con nodos por época y fondo de espacio con estrellas en movimiento.
- Barra de progreso como sable de luz: viaje de color azul → verde → morado → rojo según el avance, encendido animado al cargar y pulso de resplandor al llegar al 100%.
- Filtros Todos / Películas / Series y modo compacto.
- Guardado automático en el navegador (localStorage) + exportar/importar progreso en JSON.
- Modal estilizado para reiniciar el progreso.

[1.3.0]: #
[1.2.0]: #
[1.1.0]: #
[1.0.1]: #
[1.0.0]: #
