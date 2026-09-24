# Resolución de Laberintos — Fuerza Bruta

**Curso:** Análisis y Diseño de Algoritmos — 2026-2  
**Integrantes:** Alejandro Bermudez, Juan David Mosquera, Santiago Restrepo  
**Entrega:** Entrega 1 — Análisis de Complejidad y Recursividad  
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