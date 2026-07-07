# 4. Autómatas y lenguajes formales

## 4.1 Máquina de estados finita (DFA)

Un **autómata finito determinístico (DFA)** acepta o rechaza una cadena de
entrada, moviéndose entre un número finito de estados según el símbolo leído.
Cada estado tiene **exactamente una** transición por cada símbolo del
alfabeto.

**Ejemplo: autómata que detecta palabras que terminan en `aba`**

```
        a               b
   ┌────────┐      ┌──────────┐
   │  S0 ●──┼──a──▶│    a     │
   └────────┘      └────┬─────┘
       ▲ b               │ b
       │                 ▼
       │            ┌──────────┐
       └─────a──────│    ab    │
                     └────┬─────┘
                          │ a
                          ▼
                    ┌──────────┐
                    │  aba (F) │
                    └──────────┘
```

Cada vez que se lee una `b` fuera de la secuencia correcta, se regresa al
estado inicial `S0`; el estado final solo se alcanza al completar `a-b-a`.

## 4.2 Autómata no determinístico (NFA)

A diferencia del DFA, un estado puede tener **cero, una o varias**
transiciones para el mismo símbolo (incluyendo transiciones a conjuntos de
estados). Esto simplifica el diseño, pero requiere convertirlo a DFA para
implementarlo directamente.

## 4.3 Conversión de NFA a DFA (construcción de subconjuntos)

**Idea:** cada estado del DFA resultante representa un **conjunto** de
estados del NFA que podrían estar activos simultáneamente.

```pseudocódigo
Construcción_de_subconjuntos(NFA)
  estado_inicial_DFA = { estado_inicial_NFA }
  agregar estado_inicial_DFA a la cola de procesamiento

  MIENTRAS haya conjuntos de estados sin procesar:
    tomar el conjunto S de la cola
    PARA cada símbolo del alfabeto:
      S' = unión de todas las transiciones de cada estado en S con ese símbolo
      SI S' no ha sido creado antes: agregarlo como nuevo estado del DFA
      crear la transición S --símbolo--> S'

  un estado del DFA es final si contiene al menos un estado final del NFA
FIN
```

**Para reforzar:** este algoritmo se conoce formalmente como el **teorema de
Rabin-Scott**, y demuestra que todo NFA tiene un DFA equivalente que acepta
exactamente el mismo lenguaje. En el peor caso, el DFA resultante puede tener
hasta $2^n$ estados si el NFA tenía $n$ estados (uno por cada subconjunto
posible), aunque en la práctica suele ser mucho menor.

## 4.4 Gramática de estructura de frase

Una gramática $G = (V_n, V_t, S, P)$ se define por:

- $V_n$: símbolos **no terminales** (variables, se pueden sustituir).
- $V_t$: símbolos **terminales** (no se pueden sustituir más).
- $S$: símbolo de inicio (un elemento especial de $V_n$).
- $P$: reglas de producción, de la forma $W_0 \to W_1$.

## 4.5 Jerarquía de Chomsky

| Tipo | Nombre | Restricción de las reglas | Autómata equivalente |
|---|---|---|---|
| 0 | Sin restricciones | Cualquier combinación en ambos lados | Máquina de Turing |
| 1 | Sensibles al contexto | El lado derecho es igual o más largo que el izquierdo | Autómata linealmente acotado |
| 2 | Libres de contexto | El lado izquierdo es una sola variable | Autómata de pila |
| 3 | Regulares | $A \to a$ o $A \to aB$ | Autómata finito (DFA/NFA) |

**Para reforzar — por qué importa esta jerarquía:** cada tipo de gramática
necesita un autómata **más potente** que el anterior para poder reconocer su
lenguaje. Por eso, antes de intentar construir un autómata finito para un
lenguaje, conviene identificar de qué tipo es: si el lenguaje requiere
"contar" y comparar cantidades arbitrariamente grandes de símbolos distintos
(por ejemplo $\{a^n b^{2n} c^{3n}\}$), ningún autómata finito ni de pila puede
reconocerlo — hace falta memoria infinita, es decir, una máquina de Turing
(el lenguaje es sensible al contexto, Tipo 1). Esto se demuestra formalmente
con el **Lema del Bombeo (Pumping Lemma)**.

## 4.6 Conversión de gramática Tipo 1 a Tipo 2

Ejemplo con las reglas `S → 0SA / 01`, `1A → 11` (Tipo 1, porque `1A → 11`
mezcla contexto). El lenguaje generado es $\{0^n1^n \mid n \ge 1\}$, que en
realidad **sí es libre de contexto**, y se puede generar con reglas más
simples (Tipo 2), donde el lado izquierdo es una sola variable:

```
S → 0S1
S → 01
```

## 4.7 Ambigüedad de gramáticas

Una gramática es **ambigua** si existe al menos una cadena que se puede
generar con **dos o más árboles de derivación distintos**.

**Ejemplo clásico:** $E \to E+E \mid E \times E \mid id$. La cadena
`id + id * id` puede derivarse como $(id+id)\times id$ o como
$id+(id\times id)$ — dos árboles distintos, dos significados distintos. Esto
es un problema real en compiladores: sin una regla de precedencia de
operadores, el resultado matemático sería ambiguo.

**Para reforzar:** la ambigüedad se elimina reescribiendo la gramática para
que codifique explícitamente la precedencia y asociatividad, por ejemplo:

```
E → E + T | T
T → T * F | F
F → id
```

Aquí la estructura de la gramática misma obliga a resolver las
multiplicaciones antes que las sumas, eliminando la ambigüedad.

## 4.8 Operaciones sobre lenguajes (con autómatas)

| Operación | Construcción |
|---|---|
| Unión ($L_1 \cup L_2$) | Autómata producto: un estado final si **al menos uno** de los dos autómatas está en estado final |
| Complemento ($\overline{L}$) | Se invierten los estados finales y no finales de un DFA **completo y determinista** |
| Intersección ($L_1 \cap L_2$) | Autómata producto: un estado final solo si **ambos** autómatas están en estado final |

**Para reforzar:** el "autómata producto" simula ambos autómatas
**simultáneamente**, usando pares de estados $(q_i, p_j)$ como los nuevos
estados. Es la misma construcción para unión e intersección — solo cambia
la condición que define cuáles pares son finales.

## 4.9 Máquina de Turing

Cada regla se describe como:

$$\delta(q_{actual}, \text{leer}) = (q_{siguiente}, \text{escribir}, \text{mover})$$

donde el movimiento es `I` (izquierda) o `D` (derecha) en la cinta.

**Diferencia clave con los autómatas finitos:** una máquina de Turing puede
**leer, escribir y moverse en ambas direcciones** sobre una cinta de memoria
ilimitada, lo que la hace capaz de reconocer cualquier lenguaje computable —
no solo los regulares o libres de contexto.

## 4.10 Autómata de pila (pushdown automaton)

Cada regla se describe como:

$$\delta(q_{actual}, \text{símbolo}, \text{cima de pila}) = (q_{siguiente}, \text{acción sobre la pila})$$

**Ejemplo (reconocer $\{a^n b^n\}$):** por cada `a` leída se apila un símbolo;
por cada `b` leída se desapila uno. Si al terminar la cadena la pila queda
solo con el símbolo de fondo, la cadena se acepta — confirma que hubo la
misma cantidad de `a` que de `b`.

**Para reforzar:** la pila es justamente lo que le da a este autómata la
capacidad de "contar" que un DFA no tiene (memoria limitada a estados fijos).
Por eso los autómatas de pila reconocen lenguajes libres de contexto (Tipo 2),
un nivel más arriba que los autómatas finitos en la jerarquía de Chomsky.
