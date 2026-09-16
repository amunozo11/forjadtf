# Forja DTF

Preparador de artes para **impresión DTF** (Direct to Film). Funciona entero en
el navegador: la imagen nunca sale de tu equipo.

🔗 **https://forjadtf.vercel.app** *(actualiza esta URL tras desplegar)*

---

## Qué resuelve

El DTF tiene un requisito que casi ningún editor respeta: **el archivo no puede
llevar píxeles semitransparentes**. El film se imprime, se espolvorea con
poliamida y se cura; el polvo se adhiere donde hay tinta. Un píxel al 40 % de
opacidad deposita poca tinta, el polvo se pega mal y la transferencia sale con
halo, con bordes pegajosos o se despega al lavar.

Esta herramienta fuerza el alfa a **0 ó 255** y resuelve los degradados con
**semitono**: puntos sólidos que se van haciendo pequeños. Cada punto es tinta
al 100 %, pero el ojo a distancia lee un difuminado limpio.

## Funciones

- **Quitar fondo** por luminosidad (ideal para dibujo a línea) o por varita
  desde las esquinas, que respeta los blancos interiores del diseño.
- **Alfa binario** con umbral ajustable. Cero semitransparencias, garantizado
  y medido en pantalla.
- **Cuatro tratamientos** según la prenda:
  - *Línea negra — prenda clara*: el trazo se imprime tal cual.
  - *Tinta blanca — prenda oscura*: rellena la silueta de blanco y deja los
    negros transparentes, así el dibujo se lee igual que el original sobre tela
    negra y gasta una fracción de la tinta.
  - *Negativo*: invierte todo el arte.
  - *Ilustración a color*: conserva los colores.
- **Semitono** en los bordes o en todo el diseño. Seis formas de punto,
  lineatura y ángulo configurables, más difusión ordenada.
- **Degradado lateral** disuelto en puntos, no en opacidad.
- **Salida por tamaño real**: fijas el ancho impreso en cm y la resolución, y
  renderiza a esos píxeles. Recorte automático a la transparencia.

## La parte que importa: píxeles por celda

Cada punto de trama se dibuja dentro de una celda de `ppp ÷ lpi` píxeles. Si la
celda es pequeña el punto no cabe, salen manchas cuadradas y se pierde el rango
tonal. Es la causa número uno de semitonos feos.

| | 300 ppp | 600 ppp | 900 ppp |
|---|---|---|---|
| 40 lpi | 7,5 ✗ | **15,0 ✓** | 22,5 ✓ |
| 45 lpi | 6,7 ✗ | **13,3 ✓** | 20,0 ✓ |
| 50 lpi | 6,0 ✗ | **12,0 ✓** | 18,0 ✓ |

**Mínimo 12 px por celda.** A 300 ppp no hay ninguna lineatura usable para
semitono; por eso la app avisa en ámbar cuando no llegas.

## Cobertura exacta del punto

El radio del punto se obtiene invirtiendo la cobertura **numéricamente**: se
muestrea la celda y se ordenan las distancias, y ese array ordenado es la
inversa exacta del área cubierta. Así el área entintada coincide con el tono
pedido en cualquier forma, con menos del 0,5 % de error. Derivar la fórmula a
mano es justo donde se cuela el fallo — la fórmula cerrada del punto redondo se
desviaba un 6 % en los tonos altos.

## Antes de mandar a imprimir

- [ ] Fondo transparente real, no blanco
- [ ] Semitransparencias: **0** (la app lo muestra en verde)
- [ ] Resolución suficiente al tamaño final de estampado
- [ ] Degradados en puntos, no en opacidad
- [ ] Trazos y tipografía por encima de 1 mm de grosor
- [ ] PNG o TIFF, **nunca JPG** (no tiene canal alfa)

---

## Desplegar

Es un sitio estático de un solo archivo. No hay build ni dependencias.

**Vercel** — importa el repo en [vercel.com/new](https://vercel.com/new).
Framework: *Other*. Deja vacíos el comando de build y el directorio de salida.

**Render** — New → Static Site. Build command vacío, publish directory `.`

**GitHub Pages** — Settings → Pages → Source: `main` / carpeta raíz.

**En local** — abre `index.html` en el navegador, sin más.

## Licencia

MIT
