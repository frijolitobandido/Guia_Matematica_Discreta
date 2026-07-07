# 6. Código de Hamming

## 6.1 ¿Para qué sirve?

El código de Hamming permite **detectar y corregir** un error de un solo bit
que ocurra durante la transmisión de datos, agregando bits extra de
verificación (**bits de paridad**) calculados a partir del mensaje original.

## 6.2 Notación Hamming (a, b)

- $a$: tamaño total de la palabra transmitida (bits de datos + bits de paridad).
- $b$: cantidad de bits de datos (el mensaje real).
- $r = a - b$: cantidad de bits de paridad necesarios.

Ejemplos: Hamming(7,4) → 4 bits de datos + 3 de paridad = 7 en total.
Hamming(15,11) → 11 bits de datos + 4 de paridad = 15 en total.

**Para reforzar (regla que el material da por hecho):** los bits de paridad
siempre se colocan en las posiciones que son **potencias de 2**: 1, 2, 4, 8,
16... El resto de posiciones se llenan con los bits del dato, en orden.

## 6.3 Calcular la palabra de código Hamming(7,4) para el dato `1011`

**Paso 1 — ubicar los bits en sus posiciones:**

| Posición | 1 | 2 | 3 | 4 | 5 | 6 | 7 |
|---|---|---|---|---|---|---|---|
| Tipo | $P_1$ | $P_2$ | $D_1$ | $P_3$ | $D_2$ | $D_3$ | $D_4$ |
| Valor | ? | ? | 1 | ? | 0 | 1 | 1 |

**Paso 2 — calcular cada bit de paridad con la regla de paridad par**
(el conteo de unos en el grupo de cada supervisor debe dar un número par):

- $P_1$ revisa las posiciones 1, 3, 5, 7 → valores 1, 0, 1 → dos unos (par) → $P_1 = 0$
- $P_2$ revisa las posiciones 2, 3, 6, 7 → valores 1, 1, 1 → tres unos (impar) → $P_2 = 1$
- $P_3$ revisa las posiciones 4, 5, 6, 7 → valores 0, 1, 1 → dos unos (par) → $P_3 = 0$

**Paso 3 — palabra final:**

| Posición | 1 | 2 | 3 | 4 | 5 | 6 | 7 |
|---|---|---|---|---|---|---|---|
| Valor | 0 | 1 | 1 | 0 | 0 | 1 | 1 |

## 6.4 Detección y corrección de un error

Supongamos que se recibe `1011101` (con un error en algún bit).

**Paso 1 — ubicar los bits recibidos:**

| Posición | 1 | 2 | 3 | 4 | 5 | 6 | 7 |
|---|---|---|---|---|---|---|---|
| Valor recibido | 1 | 0 | 1 | 1 | 1 | 0 | 1 |

**Paso 2 — recalcular cada supervisor (el "síndrome"):** 0 si el grupo sigue
siendo par, 1 si es impar.

- $C_1$ (posiciones 1,3,5,7): valores 1,1,1,1 → cuatro unos (par) → $C_1 = 0$
- $C_2$ (posiciones 2,3,6,7): valores 0,1,0,1 → dos unos (par) → $C_2 = 0$
- $C_3$ (posiciones 4,5,6,7): valores 1,1,0,1 → tres unos (impar) → $C_3 = 1$

**Paso 3 — localizar el error:** se arma el número binario $C_3C_2C_1 = 100$,
que en decimal es $4$. **El bit en la posición 4 está mal.**

**Paso 4 — corregir:** se invierte el bit de la posición 4 (de 1 a 0).

- Palabra recibida (con error): `1 0 1 1 1 0 1`
- Palabra corregida: `1 0 1 0 1 0 1`

## 6.5 Por qué funciona (para reforzar)

Cada bit de paridad $P_k$ vigila exactamente las posiciones cuya
representación binaria tiene un 1 en el bit correspondiente a $k$. Por
ejemplo, $P_1$ (posición 1 = binario `001`) vigila todas las posiciones cuyo
bit menos significativo es 1 (3, 5, 7, ...); $P_2$ (posición 2 = binario
`010`) vigila las posiciones cuyo segundo bit es 1 (3, 6, 7, ...). Cuando hay
un error en la posición $x$, **todos y solo los supervisores que vigilan a
$x$** detectan la inconsistencia, y el patrón de supervisores que fallan
(el síndrome) reconstruye exactamente el número binario de $x$. Esta es la
razón matemática por la que basta con leer el síndrome como un número binario
para saber qué posición corregir, sin necesidad de revisar bit por bit.
