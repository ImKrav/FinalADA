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

Se definieron 3 escenarios de prueba de escala progresiva:

| Caso | Dimensiones | Coordenadas Inicio | Coordenadas Fin |
|---|---|---|---|
| **Pequeño** | $4 \times 4$ | `(0, 0)` | `(3, 3)` |
| **Mediano** | $8 \times 8$ | `(0, 0)` | `(7, 7)` |
| **Grande** | $12 \times 12$ | `(0, 0)` | `(11, 11)` |

---

## 4. Análisis de Complejidad

### 4.1 Complejidad Temporal: $O(4^{N \times M})$
- **Justificación:** En el peor caso (un laberinto abierto sin paredes donde la meta está al final o no existe camino), desde cada una de las casillas transitables se pueden intentar hasta 4 ramificaciones recursivas. El árbol de recursión tiene un factor de ramificación de 4 y una profundidad máxima acotada por el número total de casillas $N \times M$, resultando en un costo exponencial en el peor escenario.

### 4.2 Complejidad Espacial: $O(N \times M)$
- **Justificación:** La memoria auxiliar está dominada por la pila de llamadas del sistema (*call stack*). En el peor escenario de recursión simple, la profundidad de la pila alcanzará a lo sumo el número de casillas transitables del laberinto ($N \times M$).

---

## 5. Medición Empírica de Tiempos

Resultados obtenidos ejecutando el script [`laberinto.py`](laberinto.py):

| Tamaño | Dimensiones | Pasos Solución | Tiempo de Ejecución (s) |
|---|---|---|---|
| Pequeño | $4 \times 4$ | 7 pasos | ~0.000011 s |
| Mediano | $8 \times 8$ | 15 pasos | ~0.000016 s |
| Grande | $12 \times 12$ | 23 pasos | ~0.000009 s |

> **Nota:** La variación mínima de microsegundos entre tamaños se debe a la posición favorable de la meta en la rama de exploración directa (abajo/derecha) para estos casos de prueba.

---

## 6. Instrucciones de Ejecución

Para ejecutar el programa directamente desde la terminal:

```bash
python laberinto.py
```