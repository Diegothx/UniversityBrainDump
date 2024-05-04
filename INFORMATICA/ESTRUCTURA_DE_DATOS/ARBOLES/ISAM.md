 ## Indexed Sequential Access Method
 Indice con estructura de directorio est´atico.
Cada p´agina del directorio es de la forma:
 ![[Pasted image 20240426153734.png]]
Se garantiza que los valores k apuntados por pi satisfacen:
ki ≤ k < ki+1
Datos (hojas) estan ordenados y sus p´aginas est´an doblemente ligadas.
Dependiendo la altura H del directorio, se le llama “H-level ISAM”.
![[Pasted image 20240426154454.png]]
![[Pasted image 20240426154513.png]]
![[Pasted image 20240426154529.png]]
![[Pasted image 20240426154544.png]]
![[Pasted image 20240426154600.png]]
![[Pasted image 20240426154615.png]]
Eliminar un elemento en ISAM
Para eliminar un valor k:
1. Llamar P = busquedaEnArbol(k, root).
2. Si P contiene a k, eliminar k en todas las p´aginas de overflow que
contengan a k (duplicados).
![[Pasted image 20240426154656.png]]![[Pasted image 20240426154706.png]]
Eficiencia de ISAM
Considere:
H: la cantidad de niveles.
V : largo m´aximo las cadenas de p´aginas de overflow.
El costo de cada operaci´on (en I/O):
Busqueda: O(H + V )
Insertar: O(H + V )
Eliminar: O(H + V )
Costo de las operaciones fuertemente influenciado por p´aginas de overflow.
¿por qu´e usar ISAM?
Desventajas:
Deficiente si la cantidad de datos aumenta.
Rendimiento pobre si hay muchas modificaciones.
Pero...
Eficiente en la busquedad (para pocas modificaciones).
● Optimizaci´on: iniciar p´aginas con un 20% de espacio libre.
Muy sencillo de implementar.
´Util para acceso concurrente.