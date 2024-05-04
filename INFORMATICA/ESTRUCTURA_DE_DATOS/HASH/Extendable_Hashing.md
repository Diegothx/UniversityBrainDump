Hashing dinamico:
	Idea (intuitiva)
	Aumentar N (#(buckets))
	dependiendo del n´umero de data-entries
Idea (intuitiva)
1. Usar un directorio de buckets principales.
2. Duplicar el tama˜no del direc. cuando haya un overflow de un bucket.
![[Pasted image 20240502171718.png]]
Profundidad global y profundidad local
Profundidad global (n):
La funci´on hn considera los n-´ultimos bits.
El directorio es de tama˜no 2n.
Profundidad local (d) para cada bucket:
hd (k) = hd (k′) para todo k, k′ en el mismo bucket.
Todos los elementos del bucket coinciden en los ´ultimos d bits.

Insertar en Extendable Hashing
Para insertar una llave k con profundidad global n:
1. Computamos hn(k) (los ´ultimos n bits de k).
2. Buscamos en el directorio el puntero p para la entrada hn(k).
3. Seguimos el puntero p al bucket B correspondiente.
4. Si hay espacio en B, insertamos k en B
5. Si NO hay espacio en B con profundidad local d:
	● Hacemos un bucket split de B.
	● Aumentamos la profundidad local de B0 y B1 a d + 1.
	● Si n > d, insertamos B0 y B1 en el directorio.
	-  Si n = d, hacemos un doblamiento del directorio.

Bucket Split
	Dado un bucket B con profundidad local d,
	dividimos sus elementos en dos buckets seg´un el bit d + 1.
	![[Pasted image 20240502172119.png]]
Insertar B0 y B1 en el directorio
![[Pasted image 20240502172228.png]]
Doblamiento del directorio
Dado un bucket B con profundidad local d = n,
hacemos un bucket split de B y duplicamos el tama˜no del directorio a 2n+1.
![[Pasted image 20240502172557.png]]
![[Pasted image 20240502172610.png]]
Uso de overflow pages
En principio, extendable hashing no hace uso de overflow pages , pero...
en algunos casos es razonable usarlas.
¿en qu´e circunstancias ser´ıa prudente usar overflow pages?

Eliminaci´on en Extendable Hashing
Para eliminar, uno busca el elemento k∗ y lo elimina del bucket.
Si el bucket queda vaci´o, extendable hashing hace merge de buckets.
● ¿cu´ando ocurre una disminuci´on de la profundidad local?
● ¿cu´ando ocurre una disminuci´on de la profundidad global?
En general, merge de bucket es poco usado.
¿por qu´e?

Eficiencia de Extendable Hashing
Considere:
D: tama˜no (en p´aginas) del directorio.
El costo de cada operaci´on (en I/O):
Search: O(1)
Insertar: O(D)