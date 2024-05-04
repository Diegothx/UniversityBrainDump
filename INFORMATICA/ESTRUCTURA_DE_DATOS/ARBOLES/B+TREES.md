B+-trees: ´Indice din´amico
Estructura similar a ISAM, pero . . .
No usa p´aginas de overflow y siempre esta balanceado.
Mantiene la eficiencia de b´usqueda: O( logB (#tuplas)) .
Procedimientos eficientes de insertar/eliminar elementos.
Todos los nodos tienen un uso m´ınimo del 50% (menos el root).
“B+-trees are by far the most important access path structure in
databases and file systems”, Gray y Reuter (1993).
![[Pasted image 20240426155330.png]]
![[Pasted image 20240426155348.png]]
![[Pasted image 20240426155401.png]]
B´usqueda y range queries en B+-trees
Misma historia que b´usqueda en ISAM (con una diferencia):
SELECT *
FROM R
WHERE A BETWEEN x AND y
1. Llamar P = busquedaEnArbol(x, root).
2. Realizar una b´usqueda binaria del mayor elemento k∗ en P con k∗ ≤ x.
3. Hacer scan desde k∗ sobre todos los valores menores o iguales a y .
Suposici´on: data keys son todos distintos (sin duplicados)
Insertar un elemento en B+-trees (orden d)
Recordar: un B+-trees siempre debe estar balanceado.
Para insertar un valor k:
1. Llamar P = busquedaEnArbol(k, root).
2. Si el espacio libre de P es menor o igual a 2d , insertar k en P.
3. En otro caso: debemos insertar k en P y hacer split de [P + k]!
donde [P + k] = k∗
1 k∗
2 . . . k∗
2d k∗
2d+1.
Insertar un elemento en B+-trees (orden d)  
Para hacer split de una p´agina de datos [P + k] de tama˜no 2d + 1:  
1. Asuma: [P + k] = k∗  
1 k∗  
2 . . . k∗  
2d k∗  
2d+1 con ki < ki+1.  
2. Divida [P + k] en dos p´aginas:  
P1 = k∗  
1 . . . k∗  
d y P2 = k∗  
d+1 . . . k∗  
2d+1  
y reemplaze P por P1, P2 en la lista doble-ligada de los datos.  
3. Seleccione el valor kd+1 como divisor de P1 y P2.  
4. Reemplace el puntero p en la p´agina P′ que apuntaba a la p´agina P  
por p1 kd+1 p2 donde p1 apunta a P1 y p2 apunta a P2.  
5. Itere sobre la p´agina de directorio P′ que apuntaba a P (split de P′).
![[Pasted image 20240426155453.png]]
![[Pasted image 20240426155506.png]]
![[Pasted image 20240426155515.png]]
![[Pasted image 20240426155525.png]]
![[Pasted image 20240426155535.png]]
![[Pasted image 20240426155547.png]]
Insertar un elemento en B+-trees (orden d)  
Para hacer split de una p´agina de directorio P de tama˜no 2d + 1:  
1. Asuma:  
P = p0 k1 p1 k2 p2 . . . p2d k2d+1 p2d+1  
con ki < ki+1.  
2. Divida P en dos p´aginas:  
P1 = p0 k1 . . . kd pd y P2 = pd+1 kd+2 . . . k2d+1 p2d+1  
y reemplaze P por P1, P2 en el directorio.  
3. Seleccione el valor kd+1 como divisor de P1 y P2.  
4. Reemplace el puntero p en la p´agina P′ que apuntaba a la p´agina P  
por p1 kd+1 p2 donde p1 apunta a P1 y p2 apunta a P2.  
5. Itere sobre la p´agina de directorio P′ que apuntaba a P (split de P′).
Split del nodo ra´ız de un B+-tree  
Split de los nodos parte desde las hojas y continua hasta la ra´ız.  
Si:  
● es necesario hacer split de todos los nodos y  
● el nodo ra´ız esta lleno,  
entonces ser´a necesario crear un nuevo nodo ra´ız.  
El nodo ra´ız es el ´unico que se le permite estar lleno al menos del 50%.  
¿qu´e ocurre con la altura del ´arbol al hacer split de la ra´ız?
![[Pasted image 20240426155625.png]]
![[Pasted image 20240426155636.png]]
![[Pasted image 20240426155647.png]]
![[Pasted image 20240426155656.png]]
![[Pasted image 20240426155704.png]]
![[Pasted image 20240426155714.png]]
Eliminar un elemento en B+-trees (orden d)
Para eliminar un valor k:
1. Llamar P = busquedaEnArbol(k, root).
2. Si el espacio usado en P es mayor o igual a d + 1 , eliminar k en P.
3. En otro caso: debemos eliminar k en P y rebalancear [P − k]!
![[Pasted image 20240426155742.png]]
![[Pasted image 20240426155755.png]]
![[Pasted image 20240426155804.png]]
![[Pasted image 20240426155812.png]]
![[Pasted image 20240426155820.png]]
![[Pasted image 20240426155828.png]]
![[Pasted image 20240426155837.png]]
![[Pasted image 20240426155846.png]]
![[Pasted image 20240426155854.png]]
![[Pasted image 20240426155904.png]]
![[Pasted image 20240426155914.png]]
![[Pasted image 20240426155922.png]]
![[Pasted image 20240426155930.png]]
![[Pasted image 20240426155942.png]]
![[Pasted image 20240426155949.png]]
![[Pasted image 20240426155958.png]]
Eficiencia de B+-trees
Considere:
T : el n´umero de tuplas.
B: el tama˜no de una p´agina.
El costo de cada operaci´on (en I/O):
Busqueda: O(log B
2
( 2⋅T
B ))
Insertar: O(log B
2
( 2⋅T
B ))
Eliminar: O(log B
2
( 2⋅T
B ))
Costo depende logaritmicamente en base B!

Duplicados en B+-trees
Suposici´on anterior: NO hay data-entries duplicados.
Si consideramos duplicados, tenemos varias opciones . . .
usar p´aginas con overflow , o
data entries extendidos con una llave compuesta (k, id) , o
permitir duplicados y flexibilizar los intervalos del directorio:
ki ≤ k < ki+1 ⇒ ki ≤ k ≤ ki+1
Optimizaciones a B+-trees y otros
Varias posibles optimizaciones para B+-trees:
Compresi´on de index keys (o directorio).
Bulk loading.