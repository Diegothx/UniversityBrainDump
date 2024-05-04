## Índice
1. Introducción
2. Tablas de hash
3. Colisiones
4. Cierre
Estructura de una tabla de hash
Para un relaci´on R y atributo X :
N de p´aginas en disco (buckets).
Cada bucket cuenta con una lista ligada de overflow pages.
Usamos una funci´on de hash h tal que:
h ∶ datatype(X ) → [0, . . . , N − 1]
![[Pasted image 20240427000748.png]]
## Recordatorio: Diccionarios

**Definición**  
Un diccionario es una estructura de datos con las siguientes operaciones:
- Asociar un valor a una llave
- Actualizar el valor asociado a una llave
- Obtener el valor asociado a una llave
- En ciertos casos, eliminar de la estructura una asociación llave-valor

Los ABB fueron nuestra primera EDD para implementar diccionarios.

## Diccionarios

- Los ABB efectivamente soportan las operaciones de diccionario.
- La complejidad de las operaciones es O(h).
- Cuando están balanceados, h ∈ O(log(n)) para n llaves almacenadas.

**Ejemplo**  
Podemos mantener pares (llave, valor) de la forma (rut, archivo).
Podemos saber si un rut está en el diccionario haciendo búsqueda por rut.
Inserción usa rut para ubicar el nodo, balanceando si es necesario.

## Funciones de hash

**Definición**  
Dado un espacio de llaves K y un natural m > 0, una función de hash se define como:
h ∶ K → {0, . . . , m − 1}
Dado k ∈ K , llamaremos valor de hash de k a la evaluación h(k).

Ejemplos (simples) de funciones de Hash
Divisi´on:
h(k) = (a ∗ k + b) mod N
Multiplicaci´on:
⌊ N ⋅ (Q ⋅ k − ⌊Q ⋅ k⌋)
´¹¹¹¹¹¹¹¹¹¹¹¹¹¹¹¹¹¹¹¹¹¹¹¹¹¹¹¹¹¹¹¹¹¹¹¹¹¹¹¹¹¹¸¹¹¹¹¹¹¹¹¹¹¹¹¹¹¹¹¹¹¹¹¹¹¹¹¹¹¹¹¹¹¹¹¹¹¹¹¹¹¹¹¹¹¶
P. decimal de Q⋅k
⌋
Para cualquier Q ∈ Q, como por ejemplo:
Q = 2
√5 + 1
(inverso del golden ratio).

## Tablas de hash

**Definición**  
Dado m > 0 y un conjunto de llaves K , una tabla de hash A es una EDD que asocia valores a llaves indexadas usando una función de hash h ∶ K → {0, . . . , m − 1}. Diremos que tal A es de tamaño m.

## Inserción con encadenamiento

Primera propuesta: encadenamiento
Cada valor guardado es un nodo de una lista ligada.
Cada colisión agrega un nodo al principio/final de la lista.

## Inserción con sondeo lineal

Segunda propuesta: direccionamiento abierto
Buscamos sistemáticamente una celda vacía.
Puede producir nuevas colisiones no previstas por h.
Una forma de buscar: el sondeo lineal inserta en la primera celda vacía a la derecha de la colisión.

## Otros sondeos

- Sondeo cuadrático
- Doble hashing

Todos ellos presentan el problema de la eliminación.

## Factor de carga

Dado que las colisiones impactan la tabla, nos interesa medir cuántos datos tenemos almacenados.

## Rehashing

Si λ es grande y ya no es aceptable, las operaciones se vuelven costosas. Una solución es hacer rehashing.

