# Centro Ilumínate — sitio web

Página de inicio de **Centro Ilumínate**, centro de terapias para niños, niñas y
adolescentes del espectro autista y otras necesidades especiales, en Las Condes,
Santiago de Chile.

## Cómo abrirlo

Son cuatro archivos autocontenidos que se enlazan entre sí. No hay dependencias,
ni build, ni servidor: basta abrir `centro-iluminate-sitio.html` en el navegador
y moverse por el menú, que lleva a las otras tres.

```
centro-iluminate-sitio.html   Página de inicio (HTML + CSS + JS en línea, ~160 KB)
terapeutas.html               Página del equipo terapéutico: filtros por especialidad,
                              fichas y ventana de perfil (~180 KB)
servicios.html                Servicios y aranceles: terapias, evaluaciones, programas
                              completos y apoyos para la familia (~160 KB)
conocenos.html                «La carta de Gloria»: la historia de la fundadora, el
                              propósito del centro y el grupo de mamás azules (~160 KB)
img/                          Imágenes optimizadas en WebP y SVG que usa el sitio
Resources/                    Archivos originales (fotos, logos, figuras decorativas)
Referencias/                  Brief del centro, paleta, inspiración visual y logos
Forum-Regular.ttf             Fuente de titulares (va incrustada en base64 en el HTML)
```

## Cómo publicarlo

Para enseñárselo al cliente por una URL hay un hospedaje temporal en Netlify.
Todo lo que sabe de él es `netlify.toml`, y el procedimiento —incluidas las
trampas que ya costaron una tarde en otro proyecto— está en
[`docs/DEPLOY.md`](docs/DEPLOY.md).

Lo que se publica **no es la raíz del repositorio**: el build arma `dist/` con
el HTML y `img/`, y deja fuera `Referencias/` y `Resources/`, que llevan
material interno del cliente.

## Convenciones del código

- **Todo en español**, incluidos los nombres de clases CSS y los comentarios
  (`.carril`, `.enc`, `.bajada`, `.conv`, `.pri`). Al agregar código nuevo hay que
  seguir esa convención y no mezclar inglés.
- El archivo está en **UTF-8 con BOM** y saltos de línea **CRLF**. Conviene editarlo
  con un editor que los preserve; `sed -i` los convierte a LF en silencio y ensucia
  el diff completo.
- Las animaciones respetan `prefers-reduced-motion`: hay una regla global que las
  anula para quien tenga esa preferencia activada.

## Estructura de la página

Portada · Respaldo (mosaico de cifras) · Nosotros · Terapias · Carril del equipo
terapéutico · Diagnóstico (ADOS-2, ABLLS-R, IVADEC) · Servicios · Principios ·
Espacio (galería) · Convenios · Contacto · Pie

## Página de terapeutas

`terapeutas.html` comparte con la portada la fuente, las variables, la cabecera,
el menú fijo y el pie —copiados tal cual—, así que **un cambio en la cabecera o
el pie de la portada hay que repetirlo en las otras tres**. Son doce listas de
enlaces en total: cabecera, barra fija y columna «Explora» del pie, por archivo. Lo propio de la página: filtros
por especialidad, una ficha por profesional y una sola ventana `<dialog>` de
perfil que se arma con el `<template>` que cada ficha lleva dentro. Siete de
las nueve fichas ya llevan fotografía en `img/terapeutas/`; las dos que faltan
—las de iniciales «CV» y «JH»— siguen con el retrato provisional, iniciales
sobre un degradado, hasta que llegue la foto oficial.

## Página Conócenos

`conocenos.html` sale del esqueleto de `servicios.html` —el más nuevo— y se lee
como una carta de Gloria Del Villar a quien acaba de llegar al sitio: papel,
capitular, una nota al margen, una nota al pie, despedida y posdata. Diez
secciones con una curva tonal deliberada, donde la única banda oscura es la del
grupo de mamás azules. No agrega ni una línea de JavaScript: usa los tres
guiones que ya trae el sitio.

Lo que el centro todavía no nos ha dado va marcado en la página con el
distintivo `.falta` (o `.falta-bloque`) y con un comentario `PENDIENTE` en el
HTML. **Ninguna de esas marcas puede sobrevivir a la publicación.** Cada sección
está armada para poder borrarse entera sin que se note.

La lista al día no se escribe acá —se desfasa en cuanto se agrega una marca—
sino que se le pide al archivo:

```
grep -n 'class="falta' conocenos.html      # los distintivos visibles
grep -n 'PENDIENTE' conocenos.html         # los comentarios para quien edita
```

Dos compromisos que se anotan acá y no en la cabeza de nadie:

- El retrato de Gloria entra como `img/gloria-del-villar.webp` y reemplaza una
  sola línea: el `<div class="retrato-pendiente">` de la sección «Quién te
  escribe». La placa ocupa exactamente la misma caja, así que no hay que tocar
  el CSS.
- La mención de Gloria como **mamá azul** va con su consentimiento escrito y es
  **reversible**: su hijo va a crecer, y el día que él pida que salga, sale sin
  discusión.

## Sistema de diseño

Paleta cálida definida como variables CSS en `:root`: neutros arena y duna, ámbar
`--boton` para acentos, cacao `#3E2A1F` para las bandas oscuras y turquesa
`--turquesa` para los distintivos. Tipografías: **Forum** para titulares y
**Figtree** para el texto.

## Imágenes

Los originales viven en `Resources/`. Las versiones que consume el sitio están en
`img/`, convertidas a WebP con Pillow. Dos fotos originales de gran peso quedaron
fuera del repositorio (ver `.gitignore`); sus versiones optimizadas sí están en `img/`.
