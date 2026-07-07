# 1. Fundamentos de grafos

## 1.1 Definición

Un grafo es una estructura $G = (V, E)$ donde:

- $V = \{v_1, v_2, \dots, v_n\}$ es el conjunto de **vértices** (o nodos).
- $E = \{e_1, e_2, \dots, e_n\}$ es el conjunto de **aristas** (conexiones entre vértices).

## 1.2 Grado de un vértice

El grado de un vértice $\delta(v)$ en una gráfica **no dirigida** es el número de aristas
incidentes en él. Ejemplo:

```
      V2
     /
V1 --
     \
      V3
```

$\delta(V_1) = 2$, $\delta(V_2) = 1$, $\delta(V_3) = 1$.

## 1.3 Teorema del apretón de manos

> La suma de los grados de todos los vértices de una gráfica es igual al doble
> del número de aristas.

$$\sum_{v \in V} \delta(v) = 2|E|$$

**Por qué es cierto**: cada arista tiene exactamente dos extremos, así que aporta
+1 al grado de cada uno de sus dos vértices, es decir, +2 al total de la suma.

**Consecuencia directa (demostración: el número de vértices de grado impar es par)**

1. Separa $V$ en $V_{par}$ (grado par) y $V_{impar}$ (grado impar).
2. $\sum_{v \in V_{par}} \delta(v) + \sum_{v \in V_{impar}} \delta(v) = 2|E|$
3. La suma de los grados pares ya es par, y $2|E|$ es par, así que al restar:
   $\sum_{v \in V_{impar}} \delta(v) = 2|E| - \sum_{v \in V_{par}} \delta(v)$ también es par.
4. Una suma de números **impares** solo da un resultado **par** si la cantidad de
   sumandos es par (impar + impar = par, pero un número impar de impares da impar).
5. Por lo tanto, $|V_{impar}|$ debe ser par. $\blacksquare$

## 1.4 Ciclo de Euler vs. ciclo de Hamilton

| | Ciclo de Euler | Ciclo de Hamilton |
|---|---|---|
| Recorre | Todas las **aristas** exactamente una vez | Todos los **vértices** exactamente una vez |
| Repetición de vértices | Permitida | No permitida (excepto el inicial al cerrar) |
| Inicio/fin | Mismo vértice | Mismo vértice |
| Se enfoca en | Aristas | Vértices |
| Condición de existencia | Grafo conexo y **todos** los vértices de grado par | No hay un criterio simple: decidirlo es NP-completo |

**Para reforzar (no estaba en el material original):** decidir si un grafo tiene
un ciclo de Euler es fácil y rápido (basta revisar los grados), pero decidir si
tiene un ciclo de Hamilton es un problema mucho más difícil computacionalmente
(pertenece a la clase de problemas NP-completos). Esa asimetría es justamente
la razón por la que existe un algoritmo directo para Euler pero no para
Hamilton.

## 1.5 Matriz de adyacencia y matriz de incidencia

Ejemplo de grafo con vértices $\{a, b, c\}$ y aristas $e_1 \dots e_5$ (incluye un bucle en $a$):

**Matriz de adyacencia** (cuenta cuántas aristas conectan directamente cada par de vértices):

| | a | b | c |
|---|---|---|---|
| a | 2 | 1 | 1 |
| b | 1 | 0 | 2 |
| c | 1 | 2 | 0 |

**Matriz de incidencia** (filas = vértices, columnas = aristas; marca si el vértice es
extremo de esa arista):

| | e1 | e2 | e3 | e4 | e5 |
|---|---|---|---|---|---|
| a | 1 | 1 | 1 | 0 | 0 |
| b | 1 | 0 | 0 | 1 | 1 |
| c | 0 | 1 | 1 | 1 | 1 |

**Regla clave:** un bucle o lazo (arista que sale y regresa al mismo vértice) cuenta
como **2** en el grado de ese vértice (por eso en la matriz de adyacencia la
diagonal de `a` vale 2).

## 1.6 Recorrido de un circuito de Euler (pseudocódigo)

```pseudocódigo
PROCEDIMIENTO Euler(vértice, Grafo)
  PARA cada arista (vértice, nodo_adyacente) en Grafo HACER
    SI (vértice, nodo_adyacente) NO está visitada ENTONCES
      marcar (vértice, nodo_adyacente) como visitada
      Euler(nodo_adyacente, Grafo)
    FIN SI
  FIN PARA
  imprimir vértice
FIN PROCEDIMIENTO
```

**Para reforzar:** este pseudocódigo es una versión simplificada de una idea muy
parecida al **algoritmo de Hierholzer**, que es el método estándar para construir
un circuito de Euler en tiempo $O(|E|)$. La idea completa es:

1. Verificar primero que el grafo sea conexo y que todos los vértices tengan
   grado par (condición necesaria y suficiente, ver sección 1.8).
2. Recorrer aristas sin repetir hasta regresar al vértice de inicio, formando un
   primer ciclo cerrado.
3. Si quedan aristas sin usar, tomar cualquier vértice del ciclo que aún tenga
   aristas libres, repetir el proceso desde ahí para formar un sub-ciclo, e
   **insertarlo** dentro del ciclo principal en ese punto.
4. Repetir hasta que no queden aristas sin visitar.

**Aplicación práctica:** un camión recolector de basura que debe pasar por cada
calle (arista) de un distrito exactamente una vez y volver al punto de partida
es un problema de circuito de Euler.

## 1.7 Existencia y listado de caminos entre nodos

```pseudocódigo
PROCEDIMIENTO EncontrarCaminos(origen, destino, Grafo, visitado, camino)
  agregar origen a camino
  marcar origen como visitado

  SI origen == destino ENTONCES
    imprimir camino
  SINO
    PARA cada vecino en Grafo[origen] HACER
      SI vecino NO está visitado ENTONCES
        EncontrarCaminos(vecino, destino, Grafo, visitado, camino)
      FIN SI
    FIN PARA
  FIN SI

  eliminar último elemento de camino
  desmarcar origen como visitado
FIN PROCEDIMIENTO
```

Este es un **backtracking** clásico: explora, y si no llega al destino por esa
rama, deshace el último paso (`eliminar` y `desmarcar`) y prueba otra rama.
Encuentra **todos** los caminos simples (sin repetir vértices) entre dos nodos,
no solo uno.

## 1.8 Demostración: unión de dos grafos de grado par no genera circuito de Euler

**Enunciado:** si dos grafos $G_1$ y $G_2$ tienen todos sus vértices de grado
par, y se conectan mediante **una sola arista** entre un vértice $u \in G_1$ y
un vértice $v \in G_2$, el grafo resultante **no** tiene circuito de Euler.

**Demostración:**

1. Antes de unir: $\delta(u)$ y $\delta(v)$ son pares.
2. Al agregar la arista $u$–$v$, ambos grados aumentan en 1: $\delta(u)$ y
   $\delta(v)$ pasan a ser **impares**.
3. El resto de los vértices no se ven afectados y siguen con grado par.
4. Resultado: el grafo unido tiene **exactamente 2 vértices de grado impar**.
5. Como la condición necesaria para un circuito de Euler es que **todos** los
   vértices tengan grado par, el grafo resultante no puede tener circuito de
   Euler. $\blacksquare$

*(Nota: si el grafo tiene exactamente 2 vértices de grado impar, sí existe un
**camino** de Euler entre esos dos vértices, pero no un **circuito** cerrado.)*

## 1.9 Demostración: condición suficiente para que exista un circuito de Euler

**Hipótesis:** $G = (V, E)$ es conexo, tiene más de un vértice, y todo vértice
tiene grado par.

**Objetivo:** demostrar que $G$ tiene un circuito de Euler.

1. **Existe al menos un ciclo simple**: partiendo de cualquier vértice $v_0$ y
   caminando por aristas no usadas, como todo vértice tiene grado par, siempre
   habrá una arista de salida disponible cada vez que se entra a un vértice
   distinto de $v_0$. El único lugar donde el recorrido puede "atascarse" es de
   vuelta en $v_0$. Esto garantiza un ciclo cerrado $C_1$.
2. **Si $C_1$ no cubre todas las aristas**, se eliminan las aristas de $C_1$ de
   $G$, obteniendo $G'$. Quitar un ciclo resta exactamente 2 al grado de cada
   vértice que atraviesa (uno por entrar, uno por salir), así que **todos los
   vértices de $G'$ siguen teniendo grado par**.
3. **Conexión de subciclos (inducción)**: como $G$ era conexo, $G'$ comparte al
   menos un vértice $u$ con $C_1$. Repitiendo el paso 1 desde $u$ dentro de
   $G'$, se obtiene un nuevo ciclo $C_2$.
4. **Fusión**: se recorre $C_1$ hasta llegar a $u$, ahí se inserta el recorrido
   completo de $C_2$, y se continúa el resto de $C_1$. El resultado es un solo
   circuito cerrado que recorre todas las aristas: el circuito de Euler.
   $\blacksquare$

## Para reforzar

- El teorema de Euler completo (necesario **y** suficiente) se resume así:
  *un grafo conexo tiene circuito de Euler si y solo si todos sus vértices
  tienen grado par*. Si exactamente 2 vértices tienen grado impar, existe un
  **camino** de Euler (no cerrado) entre esos dos vértices. Si hay más de 2
  vértices de grado impar, no existe ni camino ni circuito de Euler.
- La condición de que el grafo sea **conexo** es indispensable y se suele dar
  por hecho en los ejercicios, pero conviene mencionarla siempre al enunciar el
  teorema completo, ya que sin conectividad el argumento de la demostración no
  funciona (podrían existir dos componentes separadas, cada una con todos sus
  vértices de grado par, pero sin forma de recorrerlas en un solo circuito).
