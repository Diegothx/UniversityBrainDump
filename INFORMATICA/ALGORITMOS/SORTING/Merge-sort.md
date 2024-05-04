Sorting de tuplas
Definici´on
Un conjunto de tuplas {t1, . . . , tn} esta ordenado (lexicogr´aficamente) con
respecto a una secuencia de atributos (k1, . . . , km) si:
ti ≤k1,...,km ti+1 ⇒ ti (k1) < ti+1(k1) ∨
(ti (k1) = ti+1(k1) ∧ ti ≤k2,...,km ti+1)

merge input : Dos listas ordenadas L1 y L2

output: Una lista ordenada L con los elementos de L1 y L2
Function merge (L1, L2)
L ∶= lista vac´ıa
while L1 y L2 son ambas NO vac´ıas do
remover el menor elemento m entre L1 y L2
agregar m al final de L
if L1 esta vac´ıa then
Remover elementos de L2 y agregar al final de L
else if L2 esta vac´ıa then
Remover elementos de L1 y agregar al final de L
return L
Importante: Merge es un algoritmo de una pasada por los datos

Merge sort: algoritmo
input : Una lista de n´umeros L = a1, . . . , an
output: L en orden creciente
Function mergesort (L)
if n = 1 then
return L
else
m ∶= ⌊n/2⌋
L1 ∶= a1, a2, . . . , am
L2 ∶= am+1, am+2, . . . , an
return merge(mergesort(L1), mergesort(L2))