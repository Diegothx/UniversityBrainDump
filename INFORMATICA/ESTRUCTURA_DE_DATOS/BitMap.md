# Bitmap Index

- Permite consultas multidimensionales.
- Basado en una codificación alternativa de las tuplas de una relación.
- Distinto a otros índices multidimensionales.

## Estructura de un bitmap index

Para una relación R:
1. Enumeramos sus tuplas del 1 a ∣R∣ de forma permanente:
   R = {t1, t2, . . . , t∣R∣}
2. Para un atributo c de R y un valor v ∈ πc (R) definimos un string binario bc v de largo ∣R∣ tal que:
   `bc v [i] = { 1 si ti (c) = v .
                0 en otro caso.`
3. Un bitmap index sobre un atributo c se define como el conjunto:
   `Bc = { bc v ∣ v ∈ πc (R) }`

![Pasted image 20240502181610.png]

## Aplicación de bitmap index

```sql
SELECT *
FROM Students
WHERE sName = 'Pedro' AND sMark = 6,0
```

¿Cómo calculamos esta consulta usando BsName y BsMark?

bsName
Pedro AND bsMark
6,0

Donde `AND` es aplicado bitwise (por cada bit):

css

`(b AND b′)[i] = b[i] ∧ b′[i] para todo i`

## Aplicación de bitmap index

sql

`SELECT * FROM Customers WHERE cAge BETWEEN 30 AND 60       AND cSalary BETWEEN 1MM AND 3.5MM`

¿Cómo calculamos esta consulta usando BcAge y BcSalary?

Resultado:

csharp

`OR {bx ∈ BcAge ∣ 30 ≤ x ≤ 60} AND OR {by ∈ BcSalary ∣ 1MM ≤ y ≤ 3.5MM}`

### ¿Cuáles son los inconvenientes de un Bitmap Index?

1. Tamaño de índice usa mucho espacio.
2. ¿Cómo insertamos/eliminamos una tupla?
3. ¿Cómo buscamos el bitmap que necesitamos en Bc?
4. ¿Cómo encontramos (al final) las tuplas del resultado?

## Compresión de bitmaps

Para n tuplas con m valores distintos tenemos:

- Tamaño de cada bitmap = n bits
- Tamaño índice = n × m bits

Peor caso m = n y tenemos que el tamaño del índice es n², pero:

- Si m es grande, entonces cada bitmap b se ve como: `b = 0 0 0 ⋯ 0 0 0 1 0 0 0 ⋯ 0 0 0 1 0 0 0 ⋯ 0 0 0`
- Si m es pequeño, entonces ∣Bc ∣ ≈ n bits.

Conclusión: ¡los bitmaps son altamente comprimibles!

## Run-length-encoding (RLE)

Deseamos comprimir largas secuencias de 0’s delimitados por 1:

`0 0 0 1 1 0 0 0 0 0 1 0 0 0b = 3 0 5 3`

Podemos guardar los largos de cada secuencia y así comprimir b.

scss

`comprimir(b) = 3053 o (11)(0)(101)(11)`

¿Cuál es el problema de guardar los largos en binario?

![Pasted image 20240502182025.png]

### Eficiencia de Run-length-encoding

Para n tuplas con m valores distintos:

- Cada secuencia de i-ceros y un uno, se codifica en: `2 ⋅ log2(i) bits`
- Si m ≈ n, entonces cada bitmap tiene aproximadamente un único 1. `2 ⋅ log2(n) bits (aprox.)`

Por lo tanto, Bc queda de tamaño `2 ⋅ n ⋅ log2(n)`. (asumiendo m ≈ n) `2 ⋅ n ⋅ log2(n) << n`

### Operaciones AND y OR en Run-Length-Encoding

Para dos bitmaps b1 y b2:

- Descomprimimos RLE (b1) y RLE (b2) en línea (de izq. a der.).
- Comparamos bit a bit ejecutando AND u OR.

Se soluciona problema de espacio.

## ¿Cómo insertamos/eliminamos una tupla en bitmap index?

### Optimización de RLE para bitmap indexes

Dado que conocemos la cantidad de tuplas, entonces podemos omitir la última secuencia de 0 en cada bitmap. Esto nos permite agregar un 1 en cualquier posición mayor o igual al último 1. Explotamos esta propiedad para hacer insert.

### ¿Cómo insertamos una tupla?

Para cada atributo c, buscamos el bitmap bc v con v el nuevo valor. Insertamos un nuevo 1 al final de bc v .

### ¿Cómo eliminamos una tupla?

Marcamos con un tombstone su posición

## ¿Cómo buscamos el bitmap que necesitamos en Bc ?

- Solución: B+-tree sobre c en Bc para encontrar bitmaps.

## ¿Cómo encontramos (al final) las tuplas del resultado?

- Solución: índice sobre la posición asignada a cada tupla.

## Conclusiones sobre bitmap index

Muy útil para:

- Consultas con filtros complejos.
- Columnas con baja cardinalidad.
- Datos con pocos updates (e.g. OLAP).

Deficientes:

- Datos con updates frecuentes.

Implementado por DB comerciales (Oracle)