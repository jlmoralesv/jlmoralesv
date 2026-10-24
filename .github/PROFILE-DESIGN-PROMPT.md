# Prompt: replicar este diseño de perfil en otro GitHub

Copia todo lo que está debajo de la línea en Claude Code, abierto en el repo
de perfil destino (`<usuario>/<usuario>`).

---

Rediseña el `README.md` de este repo de perfil de GitHub siguiendo el sistema de
diseño descrito abajo. Antes de escribir nada, pregúntame en **una sola tanda**:

1. Nombre a mostrar y usuario de GitHub
2. Los tres roles del titular (ej. `Full Stack / Cloud Architect / AI-First Engineer`)
3. Una línea de bio y 5–7 bullets de "About me"
4. Stack real a dibujar (lenguajes, frontend, datos, cloud, tooling)
5. Redes: LinkedIn, X, email, WhatsApp, web — y cuáles omitir
6. Si el stack **no** es cloud/AI, qué debe mostrar el diagrama de arquitectura

No inventes datos de la persona. Si falta algo, pregunta en lugar de rellenar.

## Principio

**Todo el arte es SVG propio versionado en el repo.** Cero `img.shields.io`, cero
GIFs de Giphy, cero badges `for-the-badge`. Las únicas URLs externas permitidas son
los servicios de stats de GitHub listados más abajo. Razón: los badges externos se
caen, cambian y se ven genéricos; un SVG en `assets/` siempre carga y es tuyo.

## Tokens de color (paleta nativa de GitHub)

Cada asset se genera **dos veces**, geometría idéntica, solo cambian estos valores:

| Rol | Light | Dark |
|---|---|---|
| texto | `#1F2328` | `#E6EDF3` |
| texto apagado | `#656D76` | `#8B949E` |
| acento | `#0969DA` | `#58A6FF` |
| borde / línea | `#D0D7DE` | `#30363D` |

Fondo **siempre transparente** — GitHub pinta el suyo. Nunca pongas un `<rect>` de fondo.

## Tipografía (dentro del SVG, sin webfonts)

```
.s{font-family:-apple-system,BlinkMacSystemFont,'Segoe UI','Noto Sans',Helvetica,Arial,sans-serif}
.m{font-family:ui-monospace,SFMono-Regular,'SF Mono',Menlo,Consolas,'Liberation Mono',monospace}
```

`.s` para todo el texto de interfaz, `.m` para los guiños de terminal.

## Manifiesto de archivos

Todos con `width="1200"` y `viewBox="0 0 1200 H"`:

| Archivo (x2: `-light` / `-dark`) | H | Contenido |
|---|---|---|
| `assets/hero` | 340 | kicker mono `hi there, I'm` + caret que parpadea, nombre 56px/700 letter-spacing -1.2, los tres roles 22px/600 en acento separados por `/` en color borde, bio 16px, fila de iconos de stack flotando, y a la derecha (centro ~930,170) un orbe de anillos que giran |
| `assets/h-<seccion>` | 56 | cabecera de sección: icono 24px trazo 1.8 (estilo lucide) + título 25px/600 + comentario mono 13px (`// whoami --verbose`) + regla horizontal hasta x=1196 + punto que la recorre |
| `assets/architecture` | 350 | diagrama Users → Frontend → APIs → Services → Data, con la capa de IA conectada a todos los niveles y las nubes anotadas arriba en mono |
| `assets/sphere` | 470 | esfera de alambre (r=207, centro 600,232) con los nombres del stack en los nodos; meridianos animando su `rx` para fingir rotación |
| `assets/footer` | 120 | dos ondas en `q` desfasadas + estrellas que titilan + mensaje de cierre |
| `assets/social/<red>.svg` | 44x46 | un icono de trazo por red, con flote propio y `animation-delay` escalonado |

Secciones mínimas: `about`, `architecture`, `stack`, `stats`, `connect`.

## Vocabulario de animación (CSS dentro de cada `<style>`)

Declara solo las clases que el archivo usa:

```css
.bob{animation:bob 3s ease-in-out infinite}@keyframes bob{50%{transform:translateY(-5px)}}
.caret{animation:c 1s steps(1) infinite}@keyframes c{50%{opacity:0}}
.flow{stroke-dasharray:3 9;animation:f 1.2s linear infinite}@keyframes f{to{stroke-dashoffset:-24}}
.spin{animation:sp 40s linear infinite;transform-origin:930px 170px}@keyframes sp{to{transform:rotate(360deg)}}
.ic{animation:ic 3s ease-in-out infinite;transform-box:fill-box;transform-origin:center}@keyframes ic{50%{transform:scale(1.12)}}
.tw{animation:tw 2.4s ease-in-out infinite}@keyframes tw{50%{opacity:.2}}
.dot{animation:d 4s ease-in-out infinite}@keyframes d{0%{transform:translateX(0);opacity:0}10%,90%{opacity:1}100%{transform:translateX(875px);opacity:0}}
```

Reglas: nada más rápido que ~1s ni más lento que ~40s; escalona los delays
(`0.18s` entre iconos hermanos); solo `transform` y `opacity`. Cuando necesites
animar un atributo geométrico (el `rx` de la esfera), usa `<animate>` SMIL —
el CSS no llega ahí.

## Patrón del README

Cada asset se inserta así, **sin excepción** — es lo que da el modo claro/oscuro:

```html
<picture>
  <source media="(prefers-color-scheme: dark)" srcset="./assets/hero-dark.svg">
  <img src="./assets/hero-light.svg" width="100%" alt="<descripción real>">
</picture>
```

Orden: hero → typing SVG → h-about + bullets → h-architecture + diagrama →
h-stack + esfera → h-stats + tarjetas → h-connect + iconos → footer.
Separadores: un `<br>` entre bloques. Nada de `-----`.

Rutas **relativas** (`./assets/...`), nunca absolutas a github.com.
`alt` descriptivo de verdad en cada `<img>`: es un README, hay gente que lo lee con lector de pantalla.

## Los tres servicios externos permitidos

Usa `<picture>` también aquí, con los tokens de cada tema en los parámetros
(`theme=transparent` + `title_color` / `text_color` / `icon_color`):

- `github-readme-stats.vercel.app/api?username=...` y `/api/top-langs/`
- `streak-stats.demolab.com/?user=...`
- `readme-typing-svg.demolab.com` (`font=Fira+Code&weight=500&size=18`, color acento)

El snake sale de `raw.githubusercontent.com/<usuario>/<usuario>/output/snake{,-dark}.svg`
y lo produce `.github/workflows/snake.yml`:

```yaml
name: Generate contribution snake
on:
  schedule: [{ cron: "0 */12 * * *" }]
  workflow_dispatch:
  push: { branches: [main] }
permissions: { contents: write }
jobs:
  snake:
    runs-on: ubuntu-latest
    steps:
      - uses: Platane/snk@v3
        with:
          github_user_name: ${{ github.repository_owner }}
          outputs: |
            dist/snake.svg?color_snake=#0969DA&color_dots=#ebedf0,#b6d4fb,#80b5f7,#3d8ef0,#0969da
            dist/snake-dark.svg?color_snake=#58A6FF&color_dots=#161b22,#0c2d6b,#1158c7,#388bfd,#79c0ff
      - uses: crazy-max/ghaction-github-pages@v4
        with:
          target_branch: output
          build_dir: dist
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

Avísame de dos cosas al terminar: el snake se ve roto hasta que el workflow corra
y cree la rama `output`, y necesita Settings → Actions → Workflow permissions en
*Read and write*.

## Antes de dar por hecho el trabajo

- Cada `-light.svg` y su `-dark.svg` tienen el mismo `width`/`height`/`viewBox` y
  difieren **solo** en los cuatro colores de la tabla.
- Ningún asset trae fondo opaco.
- Todo `srcset`/`src` del README apunta a un archivo que existe.
- Ningún SVG referencia una fuente o imagen externa.
- Un commit con el README, `assets/` y el workflow juntos.
