# 5. Notación y expresiones aritméticas
Incompleto
## 5.1 Los tres tipos de notación

| Notación | Posición del operador | Ejemplo |
|---|---|---|
| Infija (normal) | En medio | `A + B` |
| Prefija (polaca) | Antes | `+ A B` |
| Postfija (polaca inversa) | Después | `A B +` |

## 5.2 Evaluar una expresión postfija con una pila

Se lee **de izquierda a derecha**. Los operandos se apilan; al encontrar un
operador, se desapilan los dos últimos valores (el primero que sale es el
operando **derecho**, el segundo el **izquierdo**), se opera, y el resultado
se vuelve a apilar.

**Ejemplo:** `5 6 2 + * 12 4 / -`

| Token | Acción | Pila |
|---|---|---|
| 5, 6, 2 | push | [5, 6, 2] |
| + | pop 2, pop 6 → 6+2=8, push | [5, 8] |
| * | pop 8, pop 5 → 5*8=40, push | [40] |
| 12, 4 | push | [40, 12, 4] |
| / | pop 4, pop 12 → 12/4=3, push | [40, 3] |
| - | pop 3, pop 40 → 40-3=37, push | [37] |

**Resultado: 37**

## 5.3 Evaluar una expresión prefija con una pila

Se lee **de derecha a izquierda**. Al encontrar un operador, se desapilan dos
valores: el primero que sale es el operando **izquierdo**, el segundo el
**derecho**.

## 5.4 Convertir de infijo a postfijo usando una pila 

**Para reforzar:** este procedimiento se conoce formalmente como el
**algoritmo shunting-yard** (patio de maniobras), inventado por Edsger
Dijkstra — el material original lo describe pero no lo nombra.

```pseudocódigo
Algoritmo_Infijo_a_Postfijo(expresión)
  Para cada carácter de la expresión, de izquierda a derecha:

    SI el carácter es un operando (letra o número):
      escribirlo directamente en la salida

    SI el carácter es un operador:
      MIENTRAS la pila no esté vacía Y el tope de la pila tenga
               prioridad mayor o igual a la del operador actual:
        sacar el operador de la pila y escribirlo en la salida
      apilar el operador actual

    SI el carácter es '(':
      apilarlo

    SI el carácter es ')':
      sacar operadores de la pila y escribirlos en la salida
      hasta encontrar el '(' correspondiente (y descartarlo, sin escribirlo)

  al terminar la expresión, sacar todos los operadores restantes de la
  pila y escribirlos en la salida

Función Prioridad(operador)
  SI operador es '+' o '-': retornar 1
  SI operador es '*' o '/': retornar 2
  SI operador es '^': retornar 3
FIN
```

**Precedencia usada:** potencia (`^`) > multiplicación/división (`*`, `/`) >
suma/resta (`+`, `-`).

**Ejemplo:** `A+(B*C-(D/E^f)*g)*H` → `A B C * D E f ^ / g * - H * +`




