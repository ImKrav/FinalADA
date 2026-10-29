# Resolución de Laberintos

**Curso:** Análisis y Diseño de Algoritmos — 2026-2  
**Integrantes:** Alejandro Bermudez, Juan David Mosquera, Santiago Restrepo  
**Entregas:** Entrega 1 — Análisis de Complejidad y Recursividad (secciones 1–6) · Entrega 2 — Cinco estrategias de diseño (sección 7)  
**Caso de uso elegido:** 3.3 Juego o Puzzle (Laberinto)  

---

## 1. Definición del Problema

El problema consiste en encontrar un camino transitable desde una casilla de inicio hasta una casilla de destino (salida) dentro de una matriz bidimensional que modela un laberinto con obstáculos.

### 1.1 Entradas
- **Tablero:** Matriz bidimensional de tamaño $N \times M$ representada directamente con emojis:
  - `⬜` (Casilla libre): Espacio transitable.
  - `⬛` (Pared): Obstáculo intransitable.
- **Punto de inicio:** Coordenadas `(x_inicio, y_inicio)`. Por convención, se inicia en `(0, 0)`.
- **Punto de destino:** Coordenadas `(x_fin, y_fin)`. Por convención, la esquina opuesta `(N-1, M-1)`.

### 1.2 Salidas
- **Confirmación:** Booleano (`True` si se encontró solución, `False` en caso contrario).
- **Tablero resuelto:** La misma matriz visualizada en consola donde el camino recorrido se reemplaza con `🟩`.
- **Métricas:** Tiempo de ejecución en segundos y cantidad total de pasos dados.

### 1.3 Restricciones
- No salirse de los límites de la matriz ($0 \le x < N$ y $0 \le y < M$).
- No pisar obstáculos (`⬛`).
- No repetir casillas dentro del camino actual para evitar ciclos infinitos.

---

## 2. Solución por Fuerza Bruta (Recursividad)

La solución implementada en [`laberinto.py`](laberinto.py) utiliza una **búsqueda exhaustiva recursiva**:

1. **Caso base de éxito:** Si la posición actual $(x, y)$ coincide con la meta $(x_{fin}, y_{fin})$, se marca la casilla con `🟩`, se guarda en la lista de pasos y se retorna `True`.
2. **Validaciones:** Si la posición excede los bordes o no es una casilla libre (`!= '⬜'`), la rama se descarta retornando `False`.
3. **Exploración ciega (orden fijo):** Se marca temporalmente la casilla como parte del camino (`🟩`) y se exploran recursivamente las 4 direcciones posibles en orden predeterminado:
   - Abajo $(x+1, y)$
   - Derecha $(x, y+1)$
   - Arriba $(x-1, y)$
   - Izquierda $(x, y-1)$
4. **Desmarcado:** Si ninguna de las 4 direcciones conduce a la salida, se desmarca la casilla devolviéndola a `⬜`, se remueve del camino y se retorna `False`.

---

## 3. Casos de Prueba

Se definieron 6 escenarios de prueba en [`laberinto.py`](laberinto.py), agrupados en dos familias:

**Casos "peine" (escala progresiva, caso menos favorable):** cada columna par es un callejón sin salida abierto de arriba a abajo, y todas las columnas solo se conectan entre sí por la fila superior. Como la exploración prueba `abajo` primero, el algoritmo debe recorrer y deshacer cada callejón completo antes de encontrar el camino real (por la fila 0 hasta la última columna), forzando trabajo proporcional al tamaño del tablero.

| Caso | Dimensiones | Coordenadas Inicio | Coordenadas Fin |
|---|---|---|---|
| **Pequeño** | $5 \times 5$ | `(0, 0)` | `(4, 4)` |
| **Mediano** | $9 \times 9$ | `(0, 0)` | `(8, 8)` |
| **Grande** | $14 \times 15$ | `(0, 0)` | `(13, 14)` |

**Casos rectangulares ($N \neq M$):** laberintos "perfectos" (un único camino posible entre dos casillas cualesquiera, generados con DFS) sobre tableros no cuadrados, garantizando que **todas** las casillas libres sean alcanzables desde el inicio.

| Caso | Dimensiones | Coordenadas Inicio | Coordenadas Fin |
|---|---|---|---|
| **Rectangular ancho** | $3 \times 15$ | `(0, 0)` | `(2, 14)` |
| **Rectangular alto** | $15 \times 3$ | `(0, 0)` | `(14, 2)` |
| **Rectangular extra ancho** | $7 \times 21$ | `(0, 0)` | `(6, 20)` |

---

## 4. Análisis de Complejidad

### 4.1 Complejidad Temporal: $O(4^{N \times M})$
- **Justificación:** En el peor caso (un laberinto abierto sin paredes donde la meta está al final o no existe camino), desde cada una de las casillas transitables se pueden intentar hasta 4 ramificaciones recursivas. El árbol de recursión tiene un factor de ramificación de 4 y una profundidad máxima acotada por el número total de casillas $N \times M$, resultando en un costo exponencial en el peor escenario.

### 4.2 Complejidad Espacial: $O(N \times M)$
- **Justificación:** La memoria auxiliar está dominada por la pila de llamadas del sistema (*call stack*). En el peor escenario de recursión simple, la profundidad de la pila alcanzará a lo sumo el número de casillas transitables del laberinto ($N \times M$).

---

## 5. Medición Empírica de Tiempos

Resultados obtenidos ejecutando el script [`laberinto.py`](laberinto.py) sobre los casos "peine" (los rectangulares no se incluyen aquí por ser aleatorios y no tener una escala progresiva comparable):

| Tamaño | Dimensiones | Pasos Solución | Tiempo de Ejecución (s) |
|---|---|---|---|
| Pequeño | $5 \times 5$ | 9 pasos | ~0.000026 s |
| Mediano | $9 \times 9$ | 17 pasos | ~0.000041 s |
| Grande | $14 \times 15$ | 28 pasos | ~0.000093 s |

> **Nota:** A diferencia de un laberinto abierto, en el caso "peine" el algoritmo sí debe agotar cada callejón sin salida (bajar hasta el fondo, fallar, deshacer el camino) antes de continuar al siguiente, por lo que el tiempo crece de forma consistente con el tamaño del tablero.

---

## 6. Instrucciones de Ejecución

Para ejecutar el programa directamente desde la terminal:

```bash
python laberinto.py
```
---

## 7. Entrega 2 — Cinco estrategias de diseño

En la Entrega 2 el problema pasa a ser **encontrar el camino más corto**, y se resuelve con cinco estrategias de diseño de algoritmos. Así se compara no solo la velocidad, sino también la calidad de la respuesta.

### 7.1 Archivos

| Archivo | Contenido |
|---|---|
| [`laberintos.ipynb`](laberintos.ipynb) | Notebook con los cinco algoritmos explicados, verificación de corrección, casos de la Entrega 1, experimento y conclusiones. |
| [`index.html`](index.html) | Presentación interactiva para la exposición. |
| [`resultados.js`](resultados.js) | Mediciones del experimento. Lo genera el notebook (sección 12) y lo lee `index.html`. |
| [`laberinto.py`](laberinto.py) | Solución de la Entrega 1; el notebook reutiliza sus casos de prueba. |

### 7.2 Las cinco estrategias

*n* = *N*·*M* es el tamaño de la entrada (casillas del tablero).

| Estrategia | Nombre conocido | Tiempo (peor caso) | Tiempo (mejor caso) | Espacio | ¿Camino más corto? |
|---|---|---|---|---|---|
| Fuerza bruta | Búsqueda exhaustiva (todos los caminos simples con DFS) | $O(4^n)$ | $O(n)$ | $O(n)$ | ✅ |
| Backtracking con poda | Ramificación y poda (*branch and bound*), con poda por dominancia | $O(n^2)$ | $O(n)$ | $O(n)$ | ✅ |
| Voraz | Búsqueda voraz «primero el mejor» (*Greedy Best-First Search*) con distancia Manhattan | $O(n \log n)$ | $O(\sqrt{n} \log n)$ | $O(n)$ | ❌ |
| Divide y vencerás | Recursión por punto medio (Hirschberg; *Divide-and-Conquer Frontier Search* de Korf) | $O(n^2)$ | $O(n)$ | $O(n)$ | ✅ |
| **Programación dinámica** | **BFS por capas = algoritmo de Lee** (equivale a Dijkstra con pasos de costo 1) | $\mathbf{O(n)}$ | $\mathbf{O(n)}$ | $\mathbf{O(n)}$ | ✅ |

El voraz **no es Dijkstra**: Dijkstra también es voraz, pero elige por la distancia ya recorrida y por eso sí garantiza el camino más corto.

El código Python usa solo construcciones básicas: listas, `for`, `while`, `if` y funciones con parámetros. Cada algoritmo se verifica automáticamente en 300 laberintos aleatorios y en un caso sin salida.

### 7.3 Experimento y resultados

Se midieron tiempo, memoria y casillas exploradas en laberintos de 11 × 11 hasta **2001 × 2001** (unos 2 millones de casillas libres), de dos tipos:
- **perfectos:** un único camino;
- **con ciclos:** 10 % de paredes extra derribadas.

Un algoritmo que tarda más de 30 s o supera su presupuesto de operaciones deja de probarse en tamaños mayores. Fuerza bruta y backtracking se prueban hasta 1001 × 1001, porque por encima la recursión pasaría del millón de niveles.

| Estrategia | Resultado principal |
|---|---|
| **Programación dinámica** | Resuelve 2001 × 2001 en ≈ 4,4 s (perfecto) y ≈ 5,4 s (con ciclos), siempre con el camino óptimo. |
| Voraz | Llega a 2001 × 2001 y es el más rápido con ciclos, pero da caminos más largos: 2 344 pasos de más en 2001 × 2001. |
| Divide y vencerás | Óptimo, pero ≈ 31 veces más lento que la PD en 1001 × 1001 (perfecto); con ciclos llega a 1501 × 1501. |
| Backtracking con poda | ≈ 2,2 veces la PD en 1001 × 1001 perfecto; con ciclos llega a 161 × 161. |
| Fuerza bruta | ≈ 1,7 veces la PD en 1001 × 1001 perfecto (un solo camino); con ciclos solo termina hasta 21 × 21. |

**Conclusión:** en cualquier caso, el algoritmo más óptimo es la **programación dinámica**. Garantiza el camino más corto, es $O(n)$ en tiempo y memoria incluso en el peor caso, y es la exacta más rápida en los laberintos grandes. Las demás solo compiten en su caso favorable: el voraz cuando basta con un camino cualquiera, y la fuerza bruta o el backtracking en laberintos pequeños o casi sin ciclos.

### 7.4 La presentación (`index.html`)

| Sección | Contenido |
|---|---|
| §1 El problema | Entradas, salidas, restricciones y la notación (*n*, *V*, *d*). |
| §2 Carrera en vivo | Los cinco algoritmos resuelven el mismo laberinto. Incluye casos de demostración (como la «trampa para el voraz»), botón «Un paso» y paredes editables con clic. |
| §3 Las cinco estrategias | Idea, nombre conocido, analogía, complejidad y código Python de cada una. |
| §4 Paso a paso | Depurador: el código Python con la línea en ejecución resaltada, variables, pila de llamadas y explicación de cada decisión, en un laberinto pequeño. |
| §5 Experimento | Tabla de mediciones y barras de «cuántas veces más lento que la PD». |
| §6 Complejidad Big O | Gráfica de complejidad temporal con peor y mejor caso, selector de rango de *n*, marcador con tabla de operaciones y explicación de cada curva. |
| §7 Veredicto | Clasificación final. |
| §8 Conclusiones | El algoritmo más óptimo en cualquier caso y el mejor según el caso favorable. |

**Atajos durante la exposición:**

| Tecla | Acción |
|---|---|
| `P` | Modo exposición: una sección por pantalla, con cronómetro. |
| `←` / `→` | Sección anterior o siguiente. |
| `N` | Guion del expositor. |
| `V` | Vista del presentador en otra ventana. |
| `L` | Puntero láser. |
| `+` / `−` | Tamaño del texto. |
| `C` / `S` | Carrera / un paso (§2). |
| `,` / `.` | Paso atrás / adelante (§4). |
| `?` | Lista completa de atajos. |

### 7.5 Cómo ejecutar

```bash
pip install notebook matplotlib numpy
```

```bash
jupyter notebook laberintos.ipynb
```

Ejecutar todas las celdas tarda unos 10–15 minutos, la mayor parte en los laberintos gigantes, y regenera `resultados.js`.

Para ver la presentación hay que abrir `index.html` en el navegador, **con `resultados.js` en la misma carpeta** (por ejemplo, descargando el repositorio completo con *Code → Download ZIP*). Las gráficas y las fuentes se cargan desde internet. Sin conexión, la carrera, el paso a paso y las tablas siguen funcionando.
