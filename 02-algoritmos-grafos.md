# 2. Algoritmos sobre grafos

## 2.1 Floyd-Warshall — ruta mínima entre **todos** los pares de nodos

**Idea central:** para cada posible nodo intermediario $k$ (de 1 a $n$), se
pregunta: *¿es más corto ir de $i$ a $j$ pasando por $k$, que la mejor ruta
conocida hasta ahora?*

$$d[i][j] = \min\big(d[i][j],\ d[i][k] + d[k][j]\big)$$

```cpp
for (k = 0; k < n; k++)
  for (i = 0; i < n; i++)
    for (j = 0; j < n; j++)
      if (matriz[i][k] != INF && matriz[k][j] != INF)
        if (matriz[i][j] > matriz[i][k] + matriz[k][j]) {
          matriz[i][j] = matriz[i][k] + matriz[k][j];
          sig[i][j] = sig[i][k]; // se guarda la ruta
        }
```

- **Complejidad:** $O(n^3)$, sin importar cuántas aristas tenga el grafo.
- **Matriz de trayectorias/predecesores** (`P*`): guarda, para cada par $(i,j)$,
  el siguiente nodo (o el predecesor, según la convención) del camino más
  corto. Se usa para reconstruir la ruta completa, no solo su costo.
- **Lectura de una ruta desde `P*`:** se sigue la cadena de predecesores desde
  el destino hacia el origen (o de siguientes desde el origen), hasta cerrar el
  camino. Ejemplo: si $P[D][B] = C$ y $P[D][C] = D$, la ruta de $D$ a $B$ es
  $D \to C \to B$.

**Para reforzar:** Floyd-Warshall funciona incluso con **pesos negativos**
(siempre que no haya ciclos de peso negativo), a diferencia de Dijkstra. Es la
elección natural cuando se necesitan las distancias entre **todos** los pares
de nodos a la vez; si solo se necesita un origen fijo, Dijkstra es más
eficiente ($O(n^2)$ o $O((n+|E|)\log n)$ con cola de prioridad).

## 2.2 Cerradura transitiva (Warshall booleano)

Misma estructura de triple `for`, pero en lugar de sumar costos, se pregunta
si **existe** un camino:

```cpp
M[i][j] = M[i][j] || (M[i][k] && M[k][j]);
```

El resultado es una matriz de **alcanzabilidad**: `M[i][j] = 1` si existe
algún camino de `i` a `j` (directo o a través de otros nodos), sin importar la
distancia.

## 2.3 Coloreado de grafos

**Objetivo:** asignar colores a los vértices de modo que dos vértices
adyacentes nunca compartan color, usando la menor cantidad de colores
posible (el **número cromático** del grafo).

**Algoritmo voraz (greedy):**

```cpp
for (i = 1; i <= n; i++) {
  color_actual = 1;
  while (true) {
    conflicto = false;
    for (vecino de i)
      if (grafo[i][vecino] == 1 && colores[vecino] == color_actual)
        conflicto = true;
    if (!conflicto) { colores[i] = color_actual; break; }
    color_actual++;
  }
}
```

Para cada vértice, se prueba el color más pequeño disponible que ningún
vecino ya use.

**Para reforzar:** este algoritmo voraz **no siempre** da el número cromático
óptimo — el resultado depende del **orden** en que se recorren los vértices.
Por eso existen variantes como el algoritmo de **Welsh-Powell**, que ordena
los vértices de mayor a menor grado antes de colorear, obteniendo mejores
resultados en la práctica. Encontrar el número cromático exacto de un grafo
en general es un problema NP-difícil.

**Aplicación típica:** asignar horarios/actividades a un grupo de personas de
modo que nadie tenga dos actividades que se solapan (cada actividad es un
vértice, y hay una arista si dos actividades no pueden coincidir en un mismo
"color" de horario).

## 2.4 Dijkstra — ruta mínima desde **un** origen

```cpp
dist[origen] = 0;
for (i = 0; i < n; i++) {
  u = nodo no visitado con menor dist[u];
  visitado[u] = true;
  for (v vecino de u)
    if (dist[u] + peso(u, v) < dist[v])
      dist[v] = dist[u] + peso(u, v);
}
```

En cada paso se "fija" el nodo no visitado más cercano al origen, y se
actualizan (**relajan**) las distancias de sus vecinos.

**Para reforzar (restricción importante que el material no menciona):**
Dijkstra **solo funciona correctamente con pesos no negativos**. Si el grafo
tiene aristas de peso negativo, el algoritmo puede dar resultados incorrectos,
porque asume que una vez que un nodo se marca como visitado, su distancia ya
es la mínima posible — algo que deja de ser cierto si una arista negativa
pudiera "acortar" el camino después. Para grafos con pesos negativos (sin
ciclos negativos) se usa el algoritmo de **Bellman-Ford** en su lugar.

## 2.5 Algoritmo de Prim — árbol de expansión mínima (por nodos)

```pseudocódigo
Algoritmo_Prim_Matriz(MatrizCostos, TotalNodos, NodoInicial)
  costo_minimo[NodoInicial] = 0
  Para i desde 1 hasta TotalNodos:
    NodoActual = nodo no visitado con menor costo_minimo
    visitado[NodoActual] = Verdadero
    Para cada vecino de NodoActual:
      Si el costo directo es menor que costo_minimo[vecino]:
        costo_minimo[vecino] = costo directo
        padre[vecino] = NodoActual
  Devolver padre
```

**Idea:** el árbol crece **nodo por nodo**, siempre agregando el vértice más
barato que conecta al árbol ya construido con el resto del grafo.

## 2.6 Algoritmo de Kruskal — árbol de expansión mínima (por aristas)

El material original solo da la idea general ("cubrir todos los nodos con el
menor costo, sin formar ciclos"). El algoritmo completo es:

```pseudocódigo
Algoritmo_Kruskal(aristas, n)
  ordenar todas las aristas de menor a mayor peso
  crear n conjuntos disjuntos, uno por cada nodo (cada nodo es su propio grupo)
  árbol = {}
  Para cada arista (u, v) en orden creciente de peso:
    Si encontrar(u) != encontrar(v):     // u y v NO están en el mismo grupo
      agregar (u, v) al árbol
      unir(u, v)                          // fusiona los dos grupos
  Devolver árbol
```

**Diferencia clave con Prim:** Prim crece un único árbol conectado paso a
paso; Kruskal considera las aristas más baratas del grafo **completo**,
usando una estructura de **conjuntos disjuntos (union-find)** para detectar
rápidamente si agregar una arista formaría un ciclo. Ambos algoritmos siempre
dan el mismo costo total mínimo, aunque el árbol resultante puede diferir si
hay empates de peso.

## 2.7 Centralidad por cercanía

**Procedimiento:**

1. Para cada nodo, calcular las rutas más cortas hacia todos los demás
   (típicamente con Floyd-Warshall) y sumar esas distancias.
2. Normalizar: $\text{centralidad}(v) = \dfrac{n-1}{\text{suma de distancias desde } v}$
3. El nodo con **mayor** valor de centralidad es el más "cercano" en promedio
   a todos los demás — el candidato ideal para, por ejemplo, un punto de
   abastecimiento o distribución.

**Para reforzar:** esta métrica se llama *closeness centrality* en la
literatura de análisis de redes. Un valor alto significa que, en promedio, la
información (o mercancía) tarda menos en llegar desde ese nodo a cualquier
otro. Es distinta de la *centralidad de intermediación* (betweenness
centrality), que mide cuántas rutas más cortas **pasan por** un nodo, no qué
tan cerca está de los demás.
