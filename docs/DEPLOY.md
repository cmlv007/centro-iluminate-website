# Despliegue — sitio de Centro Ilumínate

> **Última actualización:** 4 de septiembre de 2026.
> Estado: **prototipo VIVO en https://centro-iluminate.netlify.app**, sin
> indexar y con el material interno del cliente comprobadamente fuera.
> Hospedaje temporal; el definitivo sigue sin decidir (Fase 2).
>
> **Ojo con los recuentos.** Las comprobaciones de más abajo se midieron el 23
> de agosto, cuando el sitio era **una** página y 36 imágenes. Hoy son cuatro
> páginas y 47 imágenes, así que aquellas cuentas **ya no son el control
> válido**: quedan como acta de ese día, y al final va la lista de lo que hay
> que volver a pedirle al servidor en el próximo despliegue.

## Qué hay que servir

Es lo primero, porque descarta la mitad de las opciones antes de elegir nada:

- **Cuatro archivos estáticos**, cada uno con el CSS, el JavaScript y la
  tipografía Forum en base64 dentro: `centro-iluminate-sitio.html` (~175 KB, se
  publica como `index.html`), `terapeutas.html` (~183 KB), `servicios.html`
  (~170 KB) y `conocenos.html` (~150 KB).
- **47 imágenes** en `img/`, en WebP y SVG, incluida la subcarpeta
  `img/terapeutas/` con los retratos de las fichas del equipo.
- **Sin build, sin dependencias, sin servidor.** No hay `package.json`. Nada que
  compilar y ningún proceso que tenga que estar corriendo.

Consecuencia directa: sirve **cualquier** hospedaje de archivos estáticos. No se
necesita Firebase App Hosting, ni un runtime, ni funciones. Es la diferencia de
fondo con `chucaw-website`, que es Next 16 con render en servidor y por eso
arrastra un runtime completo.

## Lo que ya está en el repositorio

Una sola cosa: **`netlify.toml`**. Está escrito para poder borrarse — ninguna
línea del sitio sabe que Netlify existe. Hace dos cosas:

1. **Arma `dist/`** con el HTML renombrado a `index.html` y la carpeta `img/`.
2. **Genera un `robots.txt`** de `Disallow: /` que no se versiona.

`dist/` y `.netlify` están en `.gitignore`.

### Por qué hay un paso de build para un sitio de archivos estáticos

Porque **la raíz del repositorio no es publicable**. Al lado del sitio viven:

| carpeta | peso | qué es |
|---|---|---|
| `Referencias/` | 3,7 MB | el brief del centro en PDF, la investigación de la competencia, los tableros de inspiración visual, los logos fuente |
| `Resources/` | 11 MB | las fotos originales sin optimizar |

Ninguna de las dos la cita el sitio — se comprobó: **cero menciones** a
`Resources/` o `Referencias/` en el HTML. Publicar la raíz entera las dejaría
accesibles por URL adivinable, incluido un PDF con información interna del
cliente. El build publica 2,3 MB en vez de 17 MB, y lo hace por esto.

### Lo que está medido, y cómo — acta del 23 de agosto de 2026

**Vale como acta de ese día, no como control vigente:** se midió cuando el sitio
era una sola página y 36 imágenes. Lo verificado entonces, sirviendo `dist/` en
un servidor local:

- **Las 36 imágenes que el HTML cita son exactamente las 36 que hay en `img/`.**
  Ni una ruta rota, ni un archivo de más.
- **La raíz responde 200** y entrega los 164 273 bytes del sitio.
- **`img/portada.webp`, `img/planta.svg`, `img/puzzle-equipo.webp` y
  `robots.txt` responden 200** desde `dist/`.
- **Control positivo — `Referencias/INFO.pdf` responde 404.** Es la prueba que
  importa: no basta con afirmar que la exclusión funciona, hay que pedir el
  archivo excluido y ver que no está.
- **El archivo llega byte a byte**: sigue en UTF-8 con BOM y saltos CRLF después
  de la copia, como exige `.gitattributes`.

## Los dos remotos y para qué es cada uno

| remoto | dónde | rol |
|---|---|---|
| `origin` | `AEONS-Agency/centro-iluminate-website` | repo de trabajo de la agencia. **El upstream de `main` apunta aquí**, así que un `git push` a secas va a este |
| `respaldo` | `Bastyasjk/centro-iluminate-website` | copia personal del operador, privada. Se empuja explícitamente |

Los dos son **privados** y los dos están en el mismo commit. Es la misma
convención que `chucaw-website`, que tiene además un tercer remoto `cliente` —
aquí no hay repositorio del cliente todavía.

Para empujar a los dos:

```
git push origin main && git push respaldo main
```

Cuidado con `git push -u respaldo main`: el `-u` **cambia el upstream de la
rama** y a partir de ahí un `git push` a secas deja de ir al repo de trabajo y
va al respaldo, sin decir nada. Pasó al crear el respaldo y se revirtió con
`git branch --set-upstream-to=origin/main main`. Empuja al respaldo nombrándolo,
sin `-u`.

Y el repo personal es privado **porque tiene que serlo**: el árbol lleva
`Referencias/` con el brief del centro y la investigación de la competencia. Es
el mismo material que el build de Netlify deja fuera a propósito. Comprobar la
visibilidad, no darla por hecha:

```
gh repo view Bastyasjk/centro-iluminate-website --json visibility
```

---

## Fase 1 — Netlify (TEMPORAL)

Una URL que enseñar al cliente mientras se decide el hospedaje definitivo y el
dominio propio.

### Dónde vive el prototipo

| | |
|---|---|
| URL | **https://centro-iluminate.netlify.app** |
| panel | https://app.netlify.com/projects/centro-iluminate |
| equipo | `centroiluminate-website` (cuenta `centroiluminate.website@gmail.com`), plan Free |
| `site_id` | `8599bcba-74be-488f-b14e-3bb3d4b6a945` |

El nombre del sitio es la URL que verá el cliente. Sigue el patrón de
`chucaw-website.netlify.app` y **no está escrito en ningún archivo del
repositorio**: cambiarlo es un clic en el panel.

### El CLI viene autenticado con la cuenta de Chucaw

Es lo primero que hay que resolver y no se ve hasta que muerde. Medido el 23 de
agosto de 2026, antes de tocar nada:

```
$ netlify status
Email: chucaw.dev@gmail.com
Teams: - chucaw-dev's team
```

Si se despliega sin cambiar de cuenta, **el sitio de Ilumínate aterriza en el
equipo de Chucaw**, mezclando dos clientes en una cuenta que no es de ninguno de
los dos.

**No se hace con `netlify logout`.** El CLI guarda varias cuentas a la vez en
`~/Library/Preferences/netlify/config.json` y alterna entre ellas; desloguearse
tira la sesión de Chucaw sin necesidad.

**Y tampoco con `netlify login` a secas.** Se probó y no hace nada: si ya hay
una sesión, se cortocircuita con «Already logged in via netlify config on your
machine» y sale con código 0. Es un éxito aparente que deja la cuenta anterior
activa — justo la forma de fallo que termina publicando en el equipo
equivocado. La segunda cuenta la añade **`--new`**:

```
netlify login --new                              # añade la cuenta del centro y la deja activa
netlify status                                   # comprobar que dice centroiluminate.website@gmail.com
netlify switch --email chucaw.dev@gmail.com      # volver a Chucaw cuando haga falta
```

`--new` abre el navegador en una URL de autorización con un ticket. Si la cuenta
de Netlify todavía no existe, esa misma página permite crearla.

Es la misma familia de errores que `pkill -f "next dev"`: un comando con más
alcance que la tarea, sobre un recurso que comparten varios proyectos de la
máquina. **Antes de desplegar cualquiera de los dos sitios, `netlify status`.**

### Este sitio se va a desplegar A MANO, y ya se sabe por qué

El repositorio es `AEONS-Agency/centro-iluminate-website`, **privado y de
organización** — comprobado con `gh repo view`. Es exactamente la combinación
que el plan gratuito de Netlify no acepta: el asistente de conexión con GitHub
llega al último paso y pide pagar. Le pasó a `chucaw-website` el 7 de agosto de
2026 y no hace falta volver a descubrirlo.

Además, para este sitio el despliegue manual es lo natural y no una concesión:
no hay build que valga la pena correr en la nube. Son tres copias de archivos.

Dos trampas más que dejó anotadas el despliegue de Chucaw y que aplican igual:

- **`netlify init` no sirve.** Necesita terminal interactiva y por debajo crea
  una deploy key en el repositorio, que la política de GitHub de la
  organización rechaza (`422 Deploy keys are disabled for this repository`).
- **Los sitios nuevos nacen con SSO puesto**, y pasó también acá. El sitio
  responde **401 en todas las rutas**, con un cuerpo que redirige a
  `app.netlify.com/edge-access`. No es contraseña del sitio (`password` viene
  `null`). Se apaga con:

  ```
  netlify api updateSite --data '{"site_id":"…","body":{"sso_login":false}}'
  ```

  Un matiz medido hoy, que corrige lo que se anotó en Chucaw: la cuenta del
  centro traía **`account_sso_login: null`** —o sea, apagado a nivel de
  cuenta— y aun así el sitio recién creado nació con **`sso_login: true`**. No
  es siempre herencia de la cuenta. **Consúltalo en el SITIO antes de
  desplegar**, no en la cuenta:

  ```
  netlify api getSite --data '{"site_id":"…"}'   # mirar sso_login
  ```

### Los pasos que se corrieron

Desde `workspace/centro-iluminate/centro-iluminate-website`, el 23 de agosto de
2026:

```
netlify login --new                      # cuenta del centro, no la de Chucaw
netlify status                           # confirmar ANTES de crear nada
netlify sites:create --name centro-iluminate --account-slug centroiluminate-website
                                         # deja el proyecto linkeado solo
netlify api getSite --data '{"site_id":"…"}'            # sso_login venía true
netlify api updateSite --data '{"site_id":"…","body":{"sso_login":false}}'
netlify deploy --build --prod
```

`--build` corre el comando de `netlify.toml`, así que `dist/` se arma sola y no
hay un segundo sitio donde mantener la lista de lo que se publica.

`sites:create` deja el proyecto linkeado por su cuenta: no hizo falta un
`netlify link` aparte.

### Verificado contra el sitio vivo

Las tres comprobaciones, corridas el 23 de agosto de 2026 justo después del
primer despliegue. Hay que repetirlas cada vez que se cambie de proveedor o de
cuenta, porque las tres dependen de configuración, no del código:

```
$ curl -sI https://centro-iluminate.netlify.app | head -1
HTTP/2 200                                   # no 401 → el SSO está apagado

$ curl -sI https://centro-iluminate.netlify.app | grep -i x-robots-tag
x-robots-tag: noindex, nofollow              # la cabecera SÍ llega

$ curl -s https://centro-iluminate.netlify.app/robots.txt
User-agent: *
Disallow: /
```

**La cabecera llega, y eso es un resultado, no un trámite.** En `chucaw-website`
se midió lo contrario: no llegaba, porque allá todas las rutas las sirve la
función SSR del runtime de Next y las reglas de cabeceras de Netlify solo
alcanzan a los archivos estáticos del CDN. Acá el sitio ES estático, no hay otra
capa, y la regla aplica. Son dos proyectos con el mismo `netlify.toml` y
resultados opuestos: la diferencia la pone qué sirve las rutas.

**El control positivo, contra el servidor real:**

```
404  /Referencias/INFO%20CENTRO%20ILUMI%CC%81NATE.pdf   ← el brief del cliente
404  /Referencias/
404  /Resources/Logo%20iluminate.svg
404  /centro-iluminate-sitio.html                       ← solo se sirve como /
404  /README.md
200  /                        164 273 b
200  /img/portada.webp        141 262 b
200  /img/planta.svg            8 044 b
200  /img/puzzle-equipo.webp   65 832 b
```

Que una carpeta no esté en `dist/` en el disco no prueba que no esté publicada:
hay que pedírsela al servidor. Ese despliegue subió **38 archivos** —
`index.html`, las 36 imágenes y `robots.txt`—, que era la cuenta exacta y ni uno
más.

### Qué hay que volver a comprobar en el próximo despliegue

El sitio pasó de una página a cuatro, así que la cuenta cambió y estas
comprobaciones están **pendientes, no hechas**:

- **52 archivos** en el despliegue: los cuatro HTML (`index.html`,
  `terapeutas.html`, `servicios.html`, `conocenos.html`), las 47 imágenes y
  `robots.txt`. Ni uno más.
- Un `200` por cada página interior, que en agosto no existían:
  `/terapeutas.html`, `/servicios.html` y `/conocenos.html`.
- Los mismos `404` de control: `/Referencias/…`, `/Resources/…`, `/README.md`, y
  `/centro-iluminate-sitio.html`, que sigue redirigiendo 301 a `/`.
- `curl -sI https://<sitio>.netlify.app | grep -i x-robots-tag`, que en agosto
  quedó sin verificar.

Y una nota de contenido, no de despliegue: `conocenos.html` lleva hoy marcas
visibles de datos que el centro todavía no entregó (el distintivo `.falta`).
**Antes de enseñar la URL como versión final hay que revisar que no quede
ninguna** — `grep -n 'class="falta' conocenos.html`.

### Para refrescar la URL más adelante

```
netlify deploy --build --prod
```

---

## Fase 2 — el hospedaje definitivo

Sin decidir, y no corre prisa: el sitio es estático, así que sirve **cualquier**
proveedor y ninguno obliga a tocar el código. Cuando haya dominio propio
(`centroiluminate.cl` u otro), las opciones son Netlify mismo con dominio
personalizado, Cloudflare Pages, GitHub Pages o Firebase Hosting a secas — que
acá sí sirve, al revés que en Chucaw.

Lo único que hay que acordarse el día del lanzamiento: **borrar `netlify.toml`
si se cambia de proveedor**, y comprobar que el `noindex` desapareció. Como el
`noindex` vive en el archivo que se borra y no dentro del HTML, desaparece solo.
