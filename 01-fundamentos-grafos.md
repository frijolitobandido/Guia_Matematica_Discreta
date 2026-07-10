# Fundamentos de grafos 


---

## 1. Definición de grafo

Un grafo es una estructura $G = (V, E)$ formada por dos conjuntos:

- $V = \{V_1, V_2, \dots, V_n\}$: el conjunto de **vértices** (o nodos).
- $E = \{e_1, e_2, \dots, e_n\}$: el conjunto de **aristas**, que son las
  conexiones entre los vértices.

Una arista no es más que un par de vértices (a veces el mismo vértice dos
veces, lo que se llama **bucle**). El grafo, en el fondo, es solo una forma
de modelar relaciones: quién está conectado con quién.

---

## 2. Grado de un vértice

**Definición:** el grado de un vértice $\delta(v)$ en una gráfica **no
dirigida** es la cantidad de aristas que llegan o salen de él (aristas
*incidentes*).

### Problema

Dado el siguiente grafo, calcula el grado de cada vértice:

```mermaid
graph TD
    V1(("V1")) --- V2(("V2"))
    V1 --- V3(("V3"))

    style V1 fill:#3f78b5,color:#fff
    style V2 fill:#dbe9f8,stroke:#3f78b5
    style V3 fill:#dbe9f8,stroke:#3f78b5
```

### Resolución

$V_1$ tiene dos aristas tocándolo (una hacia $V_2$, otra hacia $V_3$), así
que $\delta(V_1)=2$. $V_2$ y $V_3$ solo tienen una arista cada uno:

$$\delta(V_1) = 2, \qquad \delta(V_2) = 1, \qquad \delta(V_3) = 1$$

**Detalle importante — bucles:** si una arista sale y regresa al **mismo**
vértice (un bucle o lazo), esa arista cuenta **doble** para el grado de ese
vértice, porque técnicamente "entra y sale" de él. Esto se ve más abajo en
la sección de matrices.

---

## 3. Ciclo de Euler vs. ciclo de Hamilton

Ambos son recorridos cerrados (empiezan y terminan en el mismo vértice),
pero se enfocan en cosas distintas:

| | Ciclo de Euler | Ciclo de Hamilton |
|---|---|---|
| Recorre | Todas las **aristas** exactamente una vez | Todos los **vértices** exactamente una vez |
| Repetición de vértices | Permitida | No permitida (excepto el inicial al cerrar) |
| Inicio/fin | Mismo vértice | Mismo vértice |
| Se enfoca en | Aristas | Vértices |
| Condición de existencia | Grafo conexo y **todos** los vértices de grado par | No hay un criterio simple: decidirlo es NP-completo |

**Por qué la asimetría:** para Euler basta con revisar los grados de los
vértices (rapidísimo). Para Hamilton no existe un atajo así — hay que
probar combinaciones, y en el peor caso eso crece exponencialmente. Por
eso hay un algoritmo directo y eficiente para construir un circuito de
Euler (ver sección 7), pero no lo hay para Hamilton.

---

## 4. Matriz de adyacencia y matriz de incidencia

- **Matriz de adyacencia:** cuenta cuántas aristas conectan *directamente*
  cada par de vértices. Es una tabla vértice × vértice.
- **Matriz de incidencia:** marca si un vértice es *extremo* de una arista
  específica. Es una tabla vértice × arista.

### Problema

Grafo con vértices $\{a,b,c\}$ y 5 aristas, una de ellas un **bucle en
$a$**:

```mermaid
graph TD
    a((a)) ---|"e1 (bucle)"| a
    a ---|e2| b((b))
    a ---|e3| c((c))
    b ---|e4| c
    b ---|e5| c

    style a fill:#dbe9f8,stroke:#3f78b5
    style b fill:#dbe9f8,stroke:#3f78b5
    style c fill:#dbe9f8,stroke:#3f78b5
```

### Resolución

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
| a | 2 | 1 | 1 | 0 | 0 |
| b | 0 | 1 | 0 | 1 | 1 |
| c | 0 | 0 | 1 | 1 | 1 |

**Regla clave:** un bucle o lazo (arista que sale y regresa al mismo vértice) cuenta
como **2** en el grado de ese vértice (por eso en la matriz de adyacencia la
diagonal de `a` vale 2), y también aparece como **2** en su propia columna de la
matriz de incidencia.

---

## 5. Teorema del apretón de manos

**Enunciado:** la suma de los grados de todos los vértices de una gráfica
es igual al doble del número de aristas.

$$\sum_{v \in V} \delta(v) = 2|E|$$

### Demostración por inducción sobre el número de nodos

Esta es una forma distinta (y muy elegante) de demostrarlo: en vez de
razonar sobre pares/impares, se demuestra **construyendo el grafo nodo por
nodo**.

**Caso base ($n = k$ nodos):** imagina una cadena simple de $k$ vértices:

```mermaid
graph LR
    V1((V1)) --- V2((V2)) --- Vk1(("...")) --- Vk((Vk))
```

Si esta cadena tiene $e_r$ aristas en total, por construcción se cumple:

$$\sum_{i=1}^{k} \delta(V_i) = 2e_r$$

**Paso inductivo ($n = k+1$ nodos):** ahora agregamos un nuevo vértice
$V_{k+1}$, conectado al final de la cadena:

```mermaid
graph LR
    V1((V1)) --- V2((V2)) --- Vk1(("...")) --- Vk((Vk)) --- Vk2(("Vk+1"))

    style Vk2 fill:#e0645c,color:#fff,stroke:#a5342c
```

Esa **única arista nueva** aporta exactamente **+2** a la suma total:
+1 al grado de $V_k$ (donde se conecta) y +1 al grado de $V_{k+1}$ (el
nodo nuevo). Entonces:

$$\sum_{i=1}^{k+1} \delta(V_i) = \sum_{i=1}^{k} \delta(V_i) + \delta(V_{k+1}) = 2e_r + 2 = 2(e_r+1) = 2e_{r+1}$$

Como el patrón se mantiene sin importar cuántos nodos se agreguen uno por
uno (hipótesis inductiva), el teorema queda demostrado para cualquier $n$:

$$\sum_{i=1}^{k} \delta(V_i) = 2e_r \qquad \blacksquare$$

**Por qué funciona esta lógica:** cada arista nueva que se agrega tiene
exactamente dos extremos, así que **siempre** suma +2 al total sin
importar en qué momento del proceso de construcción se agregue. Por eso la
suma de grados nunca puede ser impar.

### Corolario: el número de vértices de grado impar es par

Esta es la consecuencia más usada del teorema:

1. Separa $V$ en $V_{par}$ (grado par) y $V_{impar}$ (grado impar).
2. $\displaystyle\sum_{v \in V_{par}} \delta(v) + \sum_{v \in V_{impar}} \delta(v) = 2|E|$
3. La suma de los grados pares ya es par, y $2|E|$ también es par. Al
   restar un número par de otro número par, el resultado sigue siendo
   par:
   $$\sum_{v \in V_{impar}} \delta(v) = 2|E| - \sum_{v \in V_{par}} \delta(v) \quad \text{(par)}$$
4. Pero una suma de números **impares** solo da un resultado **par** si la
   **cantidad** de sumandos es par (impar + impar = par; un número impar
   de impares siempre da impar).
5. Por lo tanto, $|V_{impar}|$ (la cantidad de vértices de grado impar)
   debe ser par. $\blacksquare$

---

## 6. Pseudocódigo de un circuito de Euler

**Problema real:** un distrito tiene camiones recolectores de basura que
deben recorrer un circuito de Euler (pasar por cada calle/arista
exactamente una vez y volver al punto de partida).

```
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

### Trazado con un ejemplo concreto

Un distrito muy simple, en forma de manzana (4 esquinas, cada una de
grado 2 — todas pares, así que sí tiene circuito de Euler):

```mermaid
graph TD
    P((P)) --- Q((Q))
    Q --- R((R))
    R --- S((S))
    S --- P

    style P fill:#6fcf7f,color:#fff
```

Llamando a `Euler(P, Grafo)`, y asumiendo que desde cada vértice se
recorren los vecinos en el orden en que aparecen (P revisa primero a Q,
luego a S):

1. `Euler(P)` → arista P–Q no visitada → marcar → `Euler(Q)`
2. `Euler(Q)` → arista Q–R no visitada → marcar → `Euler(R)`
3. `Euler(R)` → arista R–S no visitada → marcar → `Euler(S)`
4. `Euler(S)` → arista S–P no visitada → marcar → `Euler(P)`
5. `Euler(P)` (segunda vez) → ya no quedan aristas sin visitar → **imprime P**
6. Vuelve a `Euler(S)` → ya no quedan aristas → **imprime S**
7. Vuelve a `Euler(R)` → ya no quedan aristas → **imprime R**
8. Vuelve a `Euler(Q)` → ya no quedan aristas → **imprime Q**
9. Vuelve a `Euler(P)` (llamada original) → la arista P–S ya está
   visitada → no quedan aristas → **imprime P**

**Secuencia impresa:** `P, S, R, Q, P`. Como el procedimiento imprime cada
vértice *después* de haber agotado sus llamadas recursivas (es un
recorrido en post-orden), la secuencia impresa queda en el **orden
inverso** al recorrido real. Leyéndola al revés se obtiene el circuito
real: $P \to Q \to R \to S \to P$ — exactamente el camino que debería
seguir el camión recolector.

---

## 7. Recorridos: caminos entre nodos

Aquí conviene distinguir dos representaciones distintas que aparecen
juntas en el material y que **no son el mismo grafo** — vale la pena
separarlas con claridad.

### 7.1 Matriz de adyacencia dirigida

$$M[i][j] = \begin{cases} 1 & \text{si hay una flecha } i \to j \\ 0 & \text{si no hay} \end{cases}$$

| | A | B | C | D |
|---|---|---|---|---|
| A | 0 | 1 | 0 | 0 |
| B | 0 | 0 | 1 | 0 |
| C | 1 | 0 | 0 | 0 |
| D | 1 | 1 | 1 | 0 |

```mermaid
graph LR
    A --> B --> C --> A
    D --> A
    D --> B
    D --> C

    style D fill:#f5a742,color:#fff
```

Aquí $A,B,C$ forman un ciclo dirigido cerrado, y $D$ es una especie de
"nodo fuente": apunta hacia los otros tres pero nadie apunta hacia él.

### 7.2 Grafo no dirigido para buscar caminos (backtracking)

El algoritmo de búsqueda de caminos usa una lista de adyacencia distinta,
que forma un ciclo simple no dirigido de 4 nodos:

```
Grafo[A] = {B, D}
Grafo[B] = {A, C}
Grafo[C] = {B, D}
Grafo[D] = {A, C}
```

```mermaid
graph LR
    A((A)) --- B((B))
    B --- C((C))
    C --- D((D))
    D --- A
```

```
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

Es un **backtracking** clásico: agrega el nodo actual al camino, prueba
cada vecino no visitado, y si una rama no llega al destino, **deshace**
el último paso (`eliminar` + `desmarcar`) para probar otra rama. A
diferencia de un simple BFS/DFS, esto encuentra **todos** los caminos
simples posibles, no solo el primero que encuentra.

**Trazado:** buscando todos los caminos de $A$ a $C$ en el grafo de
arriba:

- Rama 1: $A \to B \to C$  (se imprime, porque llegó al destino)
- Vuelve atrás, prueba la otra rama desde $A$: $A \to D \to C$  (también
  llega)

**Resultado:** existen exactamente **2 caminos simples** entre $A$ y $C$:
$A{-}B{-}C$ y $A{-}D{-}C$ — tiene sentido, porque $A,B,C,D$ forman un
ciclo, y desde cualquier nodo hay exactamente dos formas de rodear el
ciclo hasta llegar al opuesto.

---

## 8. Demostración: unión de dos grafos de grado par no genera circuito de Euler

**Enunciado:** si $G_1$ y $G_2$ tienen todos sus vértices de grado par, y
se conectan mediante **una sola arista** entre un vértice $u \in G_1$ y un
vértice $v \in G_2$, el grafo resultante **no** tiene circuito de Euler.

```mermaid
graph TD
    subgraph G1["G1 (todos grado par)"]
        u((u)) --- p1((p1))
        p1 --- p2((p2))
        p2 --- p3((p3))
        p3 --- u
    end

    subgraph G2["G2 (todos grado par)"]
        v((v)) --- q1((q1))
        q1 --- q2((q2))
        q2 --- q3((q3))
        q3 --- v
    end

    u ---|"arista nueva u–v"| v

    style u fill:#e0645c,color:#fff,stroke:#a5342c
    style v fill:#e0645c,color:#fff,stroke:#a5342c
```

### Demostración

1. Antes de unir: $\delta(u)$ y $\delta(v)$ son pares (por hipótesis).
2. Al agregar la arista $u$–v, **ambos** grados aumentan en 1:
   $$\delta(u) = \text{par}+1 = \text{impar}, \qquad \delta(v) = \text{par}+1 = \text{impar}$$
3. El resto de los vértices no se ven afectados — siguen con grado par.
4. Resultado: el grafo unido tiene **exactamente 2 vértices de grado
   impar** ($u$ y $v$, resaltados en rojo).
5. Como la condición necesaria para un circuito de Euler es que **todos**
   los vértices tengan grado par, el grafo resultante no puede tener
   circuito de Euler. 

**Nota:** con exactamente 2 vértices de grado impar sí existe un **camino**
de Euler entre $u$ y $v$ (uno abierto, que no regresa al inicio) — pero
nunca un **circuito** (cerrado).

---

## 9. Demostración: condición suficiente para que exista un circuito de Euler

**Enunciado:** si $G=(V,E)$ es conexo, tiene más de un vértice, y **todos**
sus vértices tienen grado par, entonces $G$ tiene un circuito de Euler.
 
**Esta demostración se hace por inducción sobre el número de aristas de $G$.**
 
### Caso base
 
Si $G$ tiene el menor número de aristas posible cumpliendo la hipótesis (un
ciclo simple, donde cada vértice tiene grado exactamente 2), entonces el
propio ciclo **es** el circuito de Euler: recorre cada arista una sola vez y
regresa al vértice inicial. El enunciado se cumple trivialmente.
 
### Hipótesis inductiva
 
Se asume que el enunciado es cierto para cualquier grafo conexo con todos los
vértices de grado par que tenga **menos aristas** que $G$.
 
### Paso inductivo
 
**Paso 1 — Existe al menos un ciclo simple dentro de $G$:** partiendo de
cualquier vértice $v_0$ y caminando por aristas no usadas, como todo vértice
tiene grado par, siempre habrá una arista de salida disponible cada vez que
se entra a un vértice distinto de $v_0$ (se "gasta" una arista al entrar,
pero como el grado es par, siempre queda otra para salir). El único lugar
donde el recorrido puede "atascarse" es de vuelta en $v_0$. Esto garantiza un
ciclo cerrado $C_1$.
 
**Paso 2 — Si $C_1$ no cubre todas las aristas de $G$:** se eliminan las
aristas de $C_1$, obteniendo $G'$. Quitar un ciclo resta exactamente 2 al
grado de cada vértice que atraviesa (uno por entrar, uno por salir), así que
**todos los vértices de $G'$ siguen teniendo grado par**. Además, $G'$ tiene
**menos aristas** que $G$ (le quitamos las de $C_1$).
 
**Paso 3 — Aplicar la hipótesis inductiva:** como $G$ era conexo, cada
componente de $G'$ comparte al menos un vértice con $C_1$ (porque antes de
quitar $C_1$ todo estaba conectado a través de esas aristas). Cada componente
de $G'$ es un grafo con todos los vértices de grado par y **menos aristas**
que $G$ — por hipótesis inductiva, cada una **tiene su propio circuito de
Euler**.
 
**Paso 4 — Fusión:** se recorre $C_1$ hasta llegar a un vértice compartido
$u$ con una componente de $G'$; ahí se inserta el circuito de Euler completo
de esa componente, y se continúa el resto de $C_1$. Repitiendo esto para cada
componente de $G'$, el resultado final es un solo circuito cerrado que
recorre **todas** las aristas de $G$: el circuito de Euler. $\blacksquare$

```mermaid
flowchart LR
    A["1. Ciclo C1<br/>desde v0"] --> B["2. Quitar C1<br/>→ G' (sigue todo par)"]
    B --> C["3. Ciclo C2<br/>en G' desde u"]
    C --> D["4. Fusión:<br/>circuito de Euler"]

    style A fill:#f39c12,color:#fff
    style B fill:#9b59b6,color:#fff
    style C fill:#2ecc71,color:#fff
    style D fill:#3498db,color:#fff
```

### Resumen del teorema completo (necesario **y** suficiente)

> Un grafo conexo tiene circuito de Euler **si y solo si** todos sus
> vértices tienen grado par.
>
> - Si exactamente **2** vértices tienen grado impar → existe un
>   **camino** de Euler (no cerrado) entre esos dos vértices.
> - Si hay **más de 2** vértices de grado impar → no existe ni camino ni
>   circuito de Euler.

La condición de que el grafo sea **conexo** es indispensable: sin
conectividad podrían existir dos componentes separadas, cada una con
todos sus vértices de grado par, pero sin forma de recorrerlas en un solo
circuito.

## 10 Recorridos y la matriz de potencias $A^r$

**Enunciado:** si $A$ es la matriz de adyacencia de una gráfica (con aristas
múltiples y lazos permitidos), entonces el número de **trayectorias
diferentes de longitud $r$** de $V_i$ a $V_j$ es igual a la entrada
$i$–j-ésima de la matriz $A^r$ (la matriz $A$ multiplicada por sí misma
$r$ veces).

### Ejemplo — grafo no dirigido
 
```mermaid
graph LR
    A((A)) --- B((B))
    B --- C((C))
    A --- C
 
    style A fill:#dbe9f8,stroke:#3f78b5
    style B fill:#dbe9f8,stroke:#3f78b5
    style C fill:#dbe9f8,stroke:#3f78b5
```
 
**Matriz de adyacencia $A$:**
 
| | A | B | C |
|---|---|---|---|
| A | 0 | 1 | 1 |
| B | 1 | 0 | 1 |
| C | 1 | 1 | 0 |
 
**Calculando $A^2 = A \times A$:**
 
| | A | B | C |
|---|---|---|---|
| A | 2 | 1 | 1 |
| B | 1 | 2 | 1 |
| C | 1 | 1 | 2 |
 
**Verificación manual:** $(A^2)[A][A]=2$ dice que hay 2 trayectorias de
longitud 2 de $A$ a sí mismo: $A{-}B{-}A$ y $A{-}C{-}A$.  Y
$(A^2)[A][B]=1$ dice que hay solo 1 trayectoria de longitud 2 de $A$ a $B$:
$A{-}C{-}B$ (no cuenta $A{-}B$ porque esa es de longitud 1, no 2). 


### Ejemplo — grafo dirigido
 
```mermaid
graph LR
    A((A)) -->|1| B((B))
    A -->|1| C((C))
    B -->|1| C
    C -->|1| A
 
    style A fill:#dbe9f8,stroke:#3f78b5
    style B fill:#dbe9f8,stroke:#3f78b5
    style C fill:#dbe9f8,stroke:#3f78b5
```
 
**Matriz de adyacencia $A$** (fila = origen, columna = destino):
 
| | A | B | C |
|---|---|---|---|
| A | 0 | 1 | 1 |
| B | 0 | 0 | 1 |
| C | 1 | 0 | 0 |
 
**Calculando $A^2$:**
 
| | A | B | C |
|---|---|---|---|
| A | 1 | 0 | 1 |
| B | 1 | 0 | 0 |
| C | 0 | 1 | 1 |
 
**Verificación manual:** $(A^2)[A][A]=1$: la única trayectoria dirigida de
longitud 2 de $A$ de vuelta a $A$ es $A\to C\to A$.  $(A^2)[C][C]=1$:
$C\to A\to C$.  $(A^2)[C][B]=1$: $C\to A\to B$.  A diferencia del caso no
dirigido, aquí el orden importa — $A^2[A][C]$ no tiene por qué ser igual a
$A^2[C][A]$ (de hecho $A^2[A][C]=1$ y $A^2[C][A]=0$).
 
### Código — cálculo de $A^r$ por multiplicación repetida de matrices
 
```cpp
#include <iostream>
using namespace std;
 
void multiplicar(int A[10][10], int B[10][10], int R[10][10], int n) {
    for (int i = 0; i < n; i++)
        for (int j = 0; j < n; j++) {
            R[i][j] = 0;
            for (int k = 0; k < n; k++)
                R[i][j] += A[i][k] * B[k][j];
        }
}
 
int main() {
    int n, r;
    int A[10][10], resultado[10][10], temp[10][10];
 
    cout << "Numero de vertices: "; cin >> n;
    cout << "Ingrese la matriz de adyacencia:\n";
    for (int i = 0; i < n; i++)
        for (int j = 0; j < n; j++) {
            cin >> A[i][j];
            resultado[i][j] = A[i][j]; // resultado = A^1
        }
 
    cout << "Longitud de trayectoria r: "; cin >> r;
    for (int paso = 1; paso < r; paso++) {   // multiplica r-1 veces más
        multiplicar(resultado, A, temp, n);
        for (int i = 0; i < n; i++)
            for (int j = 0; j < n; j++)
                resultado[i][j] = temp[i][j];
    }
 
    cout << "\nMatriz A^" << r << " (numero de trayectorias de longitud " << r << "):\n";
    for (int i = 0; i < n; i++) {
        for (int j = 0; j < n; j++) cout << resultado[i][j] << "\t";
        cout << endl;
    }
    return 0;
}
```
 
Este mismo código sirve **tanto para grafos dirigidos como no dirigidos** —
la única diferencia está en cómo se construye la matriz `A` de entrada: si
el grafo es no dirigido, `A` sale simétrica ($A[i][j]=A[j][i]$); si es
dirigido, no tiene por qué serlo.
