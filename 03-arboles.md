# 3. Árboles

## 3.1 Definición y terminología

Un árbol es un grafo (no dirigido) que es:

- **Conexo** (hay un camino entre cualquier par de nodos), y
- **Acíclico** (no tiene ciclos).

Consecuencia: existe **exactamente un** camino entre cualquier par de
vértices (ver demostración en 3.5).

| Término | Significado |
|---|---|
| Raíz | Nodo principal, sin padre |
| Padre de B | Nodo del que "cuelga" B |
| Hijos de A | Nodos que cuelgan directamente de A |
| Hoja | Nodo sin hijos |
| Altura $h(v)$ | Distancia (en aristas) desde $v$ hasta la hoja más lejana de su subárbol |

## 3.2 Teorema del m-árbol completo: $|V| = m \cdot i + 1$

Un **m-árbol completo** es aquel en que todo nodo interno tiene exactamente
$m$ hijos.

**Demostración por inducción sobre $i$ (número de nodos internos):**

**Caso base ($i=0$):** sin nodos internos, el árbol es solo la raíz, así que
$|V|=1$. La fórmula da $m \cdot 0 + 1 = 1$. ✔

**Hipótesis inductiva:** para un árbol con $k$ nodos internos, $|V_k| = m\cdot k + 1$.

**Paso inductivo:** al convertir una hoja en nodo interno se le agregan
exactamente $m$ hijos nuevos. Esto aumenta los nodos internos de $k$ a $k+1$,
y el total de vértices en $m$ (los hijos nuevos):

$$|V_{k+1}| = |V_k| + m = (m\cdot k + 1) + m = m\cdot(k+1) + 1 \qquad\blacksquare$$

## 3.3 Relación $n = i + l$

Todo nodo de un árbol es **interno** (tiene hijos) o **hoja** (no tiene hijos)
— no hay una tercera categoría. Por lo tanto, el total de nodos $n$ es
simplemente la suma:

$$n = i + l$$

**Combinando con 3.2:** igualando $m\cdot i + 1 = i + l$ y despejando:

$$i = \frac{l-1}{m-1} \qquad\qquad n = \frac{ml-1}{m-1}$$

## 3.4 Cota de hojas según la altura: $l \le m^h$

**Caso base ($h=0$):** un árbol de altura 0 es un único nodo, que además es
hoja: $l=1$. La fórmula da $m^0=1$. ✔

**Hipótesis inductiva:** un árbol de altura $k$ tiene, como máximo, $m^k$ hojas.

**Paso inductivo:** un árbol de altura $k+1$ tiene, como máximo, $m$ subárboles
(uno por cada hijo de la raíz), y cada uno tiene altura máxima $k$. Por la
hipótesis, cada subárbol aporta como máximo $m^k$ hojas. Sumando los $m$
subárboles:

$$l \le \underbrace{m^k + m^k + \dots + m^k}_{m\ \text{veces}} = m \cdot m^k = m^{k+1} \qquad\blacksquare$$

## 3.5 Camino único entre dos vértices

**Existencia:** por definición, un árbol es conexo, así que siempre existe
al menos un camino entre dos vértices cualesquiera $a$ y $b$.

**Unicidad (por reducción al absurdo):** supongamos que existen dos caminos
distintos $C_1$ y $C_2$ entre $a$ y $b$. Ambos comienzan en $a$ y terminan en
$b$, así que en algún punto se separan y luego vuelven a coincidir. Recorrer
$C_1$ desde el punto de separación hasta el de reencuentro, y regresar por
$C_2$, forma un **ciclo** — pero un árbol es acíclico por definición. Esto es
una contradicción, así que el camino debe ser único. $\blacksquare$

## 3.6 Cantidad de nodos en un m-árbol totalmente completo

Contando nodo por nivel: el nivel 0 (raíz) tiene $1=m^0$ nodo, el nivel 1
tiene $m^1$, el nivel 2 tiene $m^2$, ..., hasta el nivel $h$ (las hojas) con
$m^h$ nodos. El total es la suma de una **serie geométrica**:

$$N = 1 + m + m^2 + \dots + m^h = \sum_{k=0}^{h} m^k = \frac{m^{h+1}-1}{m-1}$$

## 3.7 Longitud de camino externo (LCE)

Suma de las distancias desde la raíz hasta cada nodo especial (hoja) del árbol:

$$LCE = \sum_{i=0}^{h} ne_i \cdot i$$

donde $ne_i$ es la cantidad de nodos especiales en el nivel $i$. La
demostración por inducción sigue el mismo patrón que 3.4: al agregar un nivel
$k+1$, se suma el aporte de los nuevos nodos especiales a la distancia
acumulada de los niveles anteriores.

## 3.8 Recorridos: preorden, inorden, posorden

Para el árbol de ejemplo:

```
        J
      /   \
     E     T
    / \   / \
   A   H M   V
```

| Recorrido | Orden de visita | Regla |
|---|---|---|
| Preorden | J, E, A, H, T, M, V | raíz → izquierda → derecha |
| Inorden | A, E, H, J, M, T, V | izquierda → raíz → derecha |
| Posorden | A, H, E, M, V, T, J | izquierda → derecha → raíz |

**Para reforzar:** el **inorden** es especialmente importante en un árbol de
búsqueda binaria, porque siempre produce los valores **ordenados de menor a
mayor** — es la forma estándar de verificar si un ABB está bien construido.

## 3.9 Árbol de búsqueda binaria (ABB)

**Propiedad:** para cada nodo, todo el subárbol izquierdo tiene valores
menores, y todo el subárbol derecho tiene valores mayores.

**Inserción:** comparar con la raíz; si el nuevo valor es menor, ir a la
izquierda; si es mayor, ir a la derecha; repetir hasta encontrar un espacio
vacío.

**Eliminación** — tres casos:

1. **Nodo hoja:** se elimina directamente.
2. **Nodo con un hijo:** el hijo ocupa el lugar del nodo eliminado.
3. **Nodo con dos hijos:** se reemplaza por su **sucesor inorden** (el menor
   valor de su subárbol derecho) o su **predecesor inorden** (el mayor valor
   de su subárbol izquierdo), y luego se elimina ese sucesor/predecesor de su
   posición original.

## 3.10 AVL: factor de equilibrio y rotaciones

**Factor de equilibrio:**

$$FE(v) = h(\text{subárbol derecho}) - h(\text{subárbol izquierdo})$$

Un árbol AVL exige que $FE \in \{-1, 0, 1\}$ en todo nodo. Si al insertar un
valor algún nodo queda con $|FE| = 2$, se debe **rotar**:

| Caso | Cuándo ocurre | Rotación |
|---|---|---|
| Izquierda-Izquierda | $FE=-2$, hijo izquierdo con $FE \le 0$ | Rotación simple a la derecha |
| Derecha-Derecha | $FE=+2$, hijo derecho con $FE \ge 0$ | Rotación simple a la izquierda |
| Izquierda-Derecha | $FE=-2$, hijo izquierdo con $FE>0$ | Rotación doble izquierda-derecha (RDI) |
| Derecha-Izquierda | $FE=+2$, hijo derecho con $FE<0$ | Rotación doble derecha-izquierda (RDD) |

**Para reforzar:** el propósito del balanceo AVL es garantizar que la altura
del árbol se mantenga en $O(\log n)$, para que las búsquedas, inserciones y
eliminaciones sean siempre rápidas, incluso en el peor caso. Un ABB normal
(sin balanceo) puede degenerar en una lista enlazada si se insertan los datos
ya ordenados, haciendo que las operaciones tarden $O(n)$ en vez de
$O(\log n)$.

## 3.11 Codificación de Huffman

**Objetivo:** asignar códigos binarios de longitud variable a un conjunto de
caracteres, usando **menos bits** para los caracteres más frecuentes, de modo
que ningún código sea prefijo de otro (esto se llama código libre de
prefijos, y permite decodificar sin ambigüedad).

**El material original muestra el árbol resultante, pero no el algoritmo
paso a paso para construirlo. Aquí se completa:**

```pseudocódigo
Algoritmo_Huffman(caracteres, frecuencias)
  crear una cola de prioridad (min-heap) con un nodo hoja por cada carácter,
  usando su frecuencia como prioridad

  MIENTRAS haya más de un nodo en la cola:
    extraer los dos nodos de menor frecuencia: A y B
    crear un nuevo nodo interno N con frecuencia = frecuencia(A) + frecuencia(B)
    hacer que A sea el hijo izquierdo de N (rama "0")
    hacer que B sea el hijo derecho de N (rama "1")
    insertar N de vuelta en la cola de prioridad

  el único nodo que queda es la raíz del árbol de Huffman
FIN Algoritmo
```

El código de cada carácter se obtiene leyendo las ramas (0 o 1) desde la raíz
hasta su hoja.

**Ejemplo con las frecuencias del curso:**

| Carácter | a | b | c | d | e | f |
|---|---|---|---|---|---|---|
| Frecuencia (miles) | 45 | 13 | 12 | 16 | 9 | 5 |

Con codificación de **longitud fija** (todas las hojas al mismo nivel) cada
carácter usa 3 bits. Con Huffman (**longitud variable**), `a` (la más
frecuente) queda con solo 1 bit, mientras que `e` y `f` (las menos
frecuentes) usan 4 bits — el promedio ponderado de bits por carácter resulta
mucho menor que con longitud fija, lo que comprime el mensaje.
