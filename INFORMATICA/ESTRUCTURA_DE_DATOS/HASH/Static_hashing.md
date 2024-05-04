Operaciones en static hashing
Para las operaciones search(key k), insert(key k), delete(key k):
1. Computar el valor h(k).
2. Acceder el bucket en la posici´on h(k).
3. Buscar, insertar, o eliminar
la tupla en la p´agina h(k) o en sus overflow pages.
Costo de cada operaci´on: 1 + #(overflow pages).
Funciones de hash y colisiones
Colisi´on: valores k1 y k2 tal que h(k1) = h(k2).
La idea es encontrar una funci´on de hash h,
tal que, para toda secuencia de valores:
k1, k2, . . . , kn
el n´umero de colisiones sea m´ınimo.
Buscamos que la funci´on de hash se comporte
de forma aleatoria sobre k1, . . . , kn

Costo de operaciones en Hash Index
Dado:
una buena selecci´on de la funci´on de hash h y
#(data-entries) ∝ B ⋅ N
(con B el tama˜no de una p´agina y N el n´umero de buckets).
se tiene que el costo de cada operaci´on es constante.
Costo de cada operaci´on ≈ #(data-entries)
B ⋅ N
¿cu´al es el problema de este costo?