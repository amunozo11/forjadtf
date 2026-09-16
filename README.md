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

### Recorte del fondo
Clave de color con **dos umbrales**: dentro del margen interior el píxel se va
entero, y entre ese margen y el exterior cae progresivamente — eso es lo que
conserva el antialias del contorno en vez de dejarlo dentado.

- **Cuentagotas** para tomar el color del fondo del propio arte.
- **Modo conectado**, que solo quita lo que toca los bordes del lienzo y
  respeta los blancos interiores del diseño, o **global** por color.
- **Limpieza del color de borde** (despill): desprenmultiplica el contorno
  hacia el color clave. Sin esto todo recorte deja un halo que sobre prenda
  oscura canta muchísimo.
- **Choke**: encoger el recorte 1-2 px se come el píxel de transición que
  siempre queda. Es práctica estándar en DTF.
- **Eliminar motas** sueltas por debajo de un tamaño.

### Semitono
- **Alcance**: ninguno, solo el degradado, o todo el diseño.
- **CMYK real**: cuatro tramas independientes con los ángulos clásicos de
  imprenta (C 15°, M 75°, Y 0°, K 45°). Los ángulos separados son lo que evita
  el muaré y produce la roseta.
- **Tamaño del punto en milímetros**, de 0,15 a 4 mm. Eliges tú lo grande o
  pequeño que lo quieres; la app te dice a cuántos lpi equivale.
- Seis formas de punto, ángulo libre.
- **Ganancia de punto** para compensar lo que la tinta engorda al curar sobre
  tela, y **punto mínimo / máximo** para descartar puntos que no agarrarían y
  evitar que las sombras se cierren en mancha.

### Degradados posicionales
Por **lados**, **radial** (con centro, radio sólido y radio final) o **lineal
en cualquier ángulo** con posición y recorrido. Todos se resuelven en puntos
sólidos, nunca en opacidad, e invertibles.

### Prenda
Ocho colores preestablecidos más selector libre, para previsualizar sobre
cualquier camiseta. Base blanca opcional bajo la trama de color.

### Tratamientos
- *Conservar colores* — con semitono en «Todo» hace la separación CMYK.
- *Una sola tinta* — color elegible, el clásico de serigrafía.
- *Tinta clara conservando la lectura* — rellena de tinta las zonas claras y
  deja las oscuras transparentes. Sobre prenda oscura el dibujo se lee igual
  que el original gastando una fracción de tinta.
- *Negativo*.

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
