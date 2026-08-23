# Centro Ilumínate — sitio web

Página de inicio de **Centro Ilumínate**, centro de terapias para niños, niñas y
adolescentes del espectro autista y otras necesidades especiales, en Las Condes,
Santiago de Chile.

## Cómo abrirlo

El sitio es un único archivo autocontenido. No hay dependencias, ni build, ni
servidor: basta abrir `centro-iluminate-sitio.html` en el navegador.

```
centro-iluminate-sitio.html   Sitio completo (HTML + CSS + JS en línea, ~160 KB)
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

## Sistema de diseño

Paleta cálida definida como variables CSS en `:root`: neutros arena y duna, ámbar
`--boton` para acentos, cacao `#3E2A1F` para las bandas oscuras y turquesa
`--turquesa` para los distintivos. Tipografías: **Forum** para titulares y
**Figtree** para el texto.

## Imágenes

Los originales viven en `Resources/`. Las versiones que consume el sitio están en
`img/`, convertidas a WebP con Pillow. Dos fotos originales de gran peso quedaron
fuera del repositorio (ver `.gitignore`); sus versiones optimizadas sí están en `img/`.
