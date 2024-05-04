Estructura de datos para almacenar relaciones en páginas.
1. Create o Destroy: Crea un heap-file de la relación.
2. Insert(record): Inserta un nuevo record al final del archivo.
3. Delete(RID): Busca la página del record y lo elimina.
4. Get(searchkey): Busca en todas las páginas los records que satisfacen el searchkey.
5. Scan: Iterar sobre todas las páginas y sobre todos los records.
##### Tuplas
![[Pasted image 20240425223317.png]]

###### Record ID
- Identificador único de cada tupla.
- Necesario para mantener unicidad de cada tupla.
- Usualmente, RID = (PageID, NumSlot).
###### Tipo de almacenamiento de tuplas
- 1 página = 1 bloque en disco = tamaño fijo (≈ 8 KB)
- Tamaño de tuplas: Tamaño fijo vs Tamaño variable.
- Atomicidad de una tupla: Cada tupla en una página (spanned). vs Una tupla en múltiples páginas (unspanned).
- Tipo de tuplas en una misma página: Solo una relación (homogeneous). vs Múltiples relaciones (non-homogeneous)
##### Paginas
![[Pasted image 20240425223928.png]]
![[Pasted image 20240425223941.png]]
¿Ventajas de este formato?
Manejo de tuplas de tama˜no variable.
Eliminaci´on de tuplas.
Mover tuplas dentro de una p´agina (indireccionamiento),
o dentro de distintas p´aginas (forwarding address).

¿c´omo almacenamos tuplas/atributos de tama˜no mayor a una p´agina?
char(n) vs. varchar(n) vs. clob(n)
BLOB = Binary Large OBject
CLOB = Character Large OBject
Soportado por la mayor´ıa de DBMS modernos.
Incluyen: imagenes, videos, textos, etc.
¿cu´al es la diferencia entre char(n), varchar(n), y clob(n)?
Implementaci´on estandar para clob:
Almacenar informaci´on del atributo en una o varias p´aginas.
Guardar puntero a p´agina en el atributo BLOB/CLOB
##### Implementacion (Tarea 1)
![[Pasted image 20240425224238.png]]
![[Pasted image 20240425224229.png]]
![[Pasted image 20240425224216.png]]
![[Pasted image 20240425224301.png]]
