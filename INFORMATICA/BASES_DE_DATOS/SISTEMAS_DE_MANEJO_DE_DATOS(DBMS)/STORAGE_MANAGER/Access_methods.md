## ![[Pasted image 20240426153108.png]]
### *Índice*

- **Procesador de consultas**
- **Almacenamiento basado en páginas**
- **Alternativas**

---

## Procesador de consultas
Procesador de consultas Considera las relaciones como “colección de records”. Administrador de almacenamiento provee al procesador de consultas con métodos para acceder estas colecciones de records. Estos métodos son conocidos como métodos de acceso.  ### Evaluación de consulta y métodos de acceso 
```sql Q = SELECT       pName, mStadium, goals     FROM       Players AS P, Matches AS M, Players Matches AS PM     WHERE       P.pId = PM.pId AND PM.mId = M.mId AND       P.pYear ≥ 1985```

Plan físico para evaluar Q:

1. PROJ(pName, mStadium, goals)
2. HASH-JOIN
3. INDEX-SELpYear ≥1985
4. P
5. NL-JOIN
6. PROJ(mId, mStadium)
7. M
8. PM

---

## Interfaz de acceso a relaciones

1. Create o Destroy: Crear o destruir el acceso.
2. Insert(record): Insertar un nuevo record a la relación.
3. Delete(RID): Eliminar un record dado su Record ID (RID).
4. Get(searchkey): Buscar un record dado una “llave de búsqueda”. El criterio de búsqueda puede ser diverso (por valor, por rango, etc). En general, Get puede estar “sobrecargado”.
5. Scan: Iterar sobre todos los records.

### Ejemplo: [[Heapfiles]]

### Ejemplo: Sortedfile

1. Create o Destroy: Crea un sortedfile de la relación.
2. Insert(record): Inserta un record en la posición que le corresponde.
3. Delete(RID): Busca la página del record y lo elimina.
4. Get(searchkey): Búsqueda binaria del record que satisface el searchkey.
5. Scan: Iterar sobre todos las páginas y sobre todos los records.

---

## Índices

Definición: Método de acceso que optimiza el acceso a los datos para una consulta o conjunto de consultas en particular.

### Algunos hechos importantes sobre un índice

1. Un índice optimiza un subconjunto de consultas.
2. Un índice optimiza ciertas consultas pero puede hacer otras más costosas.
3. Es posible sacrificar espacio por tiempo, pero idealmente un índice debe ser mantenido en RAM.

### ¿Qué consultas nos gustaría optimizar con índices?

- Búsqueda por valor (value query).
- Búsqueda por rango (range query).
- Búsqueda por match (pattern matching).

### Evaluación de la eficiencia de índices

Se evalúa con respecto a:

- Tipo de acceso.
- Tiempo de acceso.
- Tiempo de inserción.
- Tiempo de eliminación.
- Sobrecarga de espacio.

### Componentes de un índice

Definición:

- Search key: Parámetros de búsqueda.
- Index entry: Valor o puntero de la estructura interna de un índice.
- Data entry: Record mismo o dirección donde se almacena un record.
Para un search key k, un data entry puede ser:

1. Un record.
2. (k, RID).
3. (k, lista de RID).
### Clustered indexes

Definición:

- Clustered index: Índice para el cual el orden de sus data entries es el mismo orden de los records en disco.
- Unclustered index: Índice que NO es clustered.
- En general, por cada relación es posible mantener un solo clustered index. Unclustered index tiene data entries del tipo 2 o 3.
- Unclustered index son ineficientes cuando el output es numeroso (range queries.)
- Usualmente, clustered y unclustered indexes son conocidos como:
	clustered index = ´ındice primario.
	unclustered index = ´ındice secundario.

### Índices densos o dispersos

Definición:

- Densos: Un index entry por cada record de la relación.
- Dispersos: No todos los records están mencionados en los index entries.

### Resumen de la clasificación de índices

Clustered vs unclustered:

- Clustered: El orden de sus data entries es el mismo que los records.
- Unclustered: Data entries no mantienen el mismo orden de los datos. Denso vs disperso:
- Denso: Un index entry por cada record.
- Disperso: No todos los records están mencionados en los index entries.

### Archivos ordenados y b´usqueda binaria  
	SELECT *  
	FROM Players  
	WHERE pAge ≥ 31  
¿c´omo hacemos esta b´usqueda en un archivo ordenado?  
B´usqueda binaria al primer elemento tal que pAge < 31.  
Retornamos todos los elementos mayores que 31.
![[Pasted image 20240426153512.png]]
Costo de la b´usqueda: log2(#pages)
### Dos tipos de índices básicos

1. Índices basados en árboles:
	![[Pasted image 20240426153546.png]]
    - [[ISAM]]: Indexed Sequential Access Method.
    - [[B+TREES]].
2. [Índices basados en hashing](Tabla_de_hash.md):
	Indices basados en Hashing
	Eficientes para consultas de valores (value query):
	SELECT *
	FROM Players
	WHERE pAge = 30
	Usado para la implementaci´on de operadores relacionales.
	Ejemplo: Index-join o Hash-join.
	Soportado en la mayor´ıa de los motores de BD.
	Hash index vs B+-trees
	B+-trees soporta range queries.
	Hash index es m´as eficiente para value queries.
	Tabla de hash en BDD:
		Para un relaci´on R y atributo X :
		N de p´aginas en disco (buckets).
		Cada bucket cuenta con una lista ligada de overflow pages.
		Usamos una funci´on de hash h tal que:
		h ∶ datatype(X ) → [0, . . . , N − 1]
	- [[Static_hashing]]
    - [[Extendable_Hashing]].
3. Indices multidimencionales:
	- multiples search key para una misma tupla.
	- cada search key es igualmente importante.
	- Consultas t´ıpicas de ´ındices multidimensionales
		1. Partial match queries.
		2. Range queries.
		3. Nearest-neighbor queries.
		4. Where-am-I queries
	- Aplicaciones de ´ındices multidimensionales
		1. Consultas SQL con filtros sobre varios campos.
		2. Datos geogr´aficos. ● GIS: Geographical Information Systems.
		3. Datos astron´omics.
		4. Dise˜no de circuitos.
		5. Dise˜no asistido por computadora.
	- B+-trees como ´ındices multidimensionales
	B+-trees est´an restrigidos a 1-dimension.
	Objetivo de ´ındices multidimensionales:
	1. sim´etrico con respecto a cada dimension.
	2. agrupe los datos seg´un su posici´on en el espacio.
	3. provee la mayor cantidad de consultas distintas.
	Muchas propuestas para ´ındices multidimensionales
		Quad Tree [Finkel 1974] K-D-B-Tree [Robinson 1981]
		R-tree [Guttman 1984] Grid File [Nievergelt 1984]
		R+-tree [Sellis 1987] LSD-tree [Henrich 1989]
		R*-tree [Geckmann 1990] hB-tree [Lomet 1990]
		Vp-tree [Chiueh 1994] TV-tree [Lin 1994]
		UB-tree [Bayer 1996] hB-pi-tree [Evangelidis 1995]
		SS-tree [White 1996] X-tree [Berchtold 1996]
		M-tree [Ciaccia 1996] SR-tree [Katayama 1997]
		Pyramid [Berchtold 1998] Hybrid-tree [Chakrabarti 1999]
		DABS-tree [B¨ohm 1999] IQ-tree [B¨ohm 2000]
		Slim-tree [Faloutsos 2000] Landmark file [B¨ohm 2000]
		P-Sphere-tree [Goldstein 2000] A-tree [Sakurai 2000]
		Ninguna de ellas entrega
		una soluci´on 100% satisfactoria en todos los casos
	Tipos de ´ındices multidimensionales
		Es posible dividir estas propuestas en dos categor´ıas:
		Basados en Hashing.
		Basados en ´arboles.
		Otros
	Bitmap index
		Permite consultas multidimensionales.
		Basado en una codificaci´on alternativa de las tuplas de una relaci´on.
		Distinto a otros ´ındices multidimensionales.
	Estructura de un bitmap index
		Para una relaci´on R:
		1. Enumeramos sus tuplas del 1 a ∣R∣ de forma permanente:
		R = {t1, t2, . . . , t∣R∣}
		2. Para un atributo c de R y un valor v ∈ πc (R) definimos un string
		binario bc
		v de largo ∣R∣ tal que:
		bc
		v [i] = { 1 si ti (c) = v .
		0 en otro caso.
		3. Un bitmap index sobre un atributo c se define como el conjunto:
		Bc = { bc
		v ∣ v ∈ πc (R) }
		![[Pasted image 20240502181610.png]]
		Aplicaci´on de bitmap index
			SELECT *
			FROM Students
			WHERE sName = ’Pedro’ AND sMark = 6,0
			¿c´omo calculamos esta consulta usando BsName y BsMark ?
			Resultado:
			bsName
			Pedro AND bsMark
			6,0
			donde AND es aplicado bitwise (por cada bit):
			(b AND b′)[i] = b[i] ∧ b′[i] para todo i
		Aplicaci´on de bitmap index
SELECT *
FROM Customers
WHERE cAge BETWEEN 30 AND 60
cSalary BETWEEN 1MM AND 3.5MM
¿c´omo calculamos esta consulta usando BcAge y BcSalary ?
Resultado:
OR {bx ∈ BcAge ∣ 30 ≤ x ≤ 60}
AND
OR {by ∈ BcSalary ∣ 1MM ≤ y ≤ 3.5MM
¿cu´ales son los inconvenientes de un Bitmap Index?
1. Tama˜no de ´ındice usa mucho espacio.
2. ¿c´omo insertamos/eliminamos una tupla?
3. ¿c´omo buscamos el bitmap que necesitamos en Bc ?
4. ¿c´omo encontramos (al final) las tuplas del resultado?
Compresi´on de bitmaps
	Para n tuplas con m valores distintos tenemos:
	Tama˜no de cada bitmap = n bits
	Tama˜no ´ındice = n × m bits
	Peor caso m = n y tenemos que el tama˜no del ´ındice es n2 , pero:
	si m es grande, entonces cada bitmap b se ve como:
	b = 0 0 0 ⋯ 0 0 0 1 0 0 0 ⋯ 0 0 0 1 0 0 0 ⋯ 0 0 0
	si m es peque˜no, entonces ∣Bc ∣ ≈ n bits.
	Conclusi´on: bitmaps son altamente comprimibles!
	Run-length-encoding (RLE)
Deseamos comprimir largas secuencias de 0’s delimitados por 1:
0 0 0 1 1 0 0 0 0 0 1 0 0 0b =
3 0 5 3
Podemos guardar los largos de cada secuencia y as´ı comprimir b.
comprimir (b) = 3053 o (11)(0)(101)(11)
¿cu´al es el problema de guardar los largos en binario?
![[Pasted image 20240502182025.png]]

Eficiencia de Run-length-encoding
Para n tuplas con m valores distintos.
Cada secuencia de i-ceros y un uno, se codifica en:
2 ⋅ log2(i) bits
Si m ≈ n, entonces cada bitmap tiene aproximadamente un ´unico 1.
2 ⋅ log2(n) bits (approx.)
Por lo tanto, Bc queda de tama˜no 2 ⋅ n ⋅ log2(n). (asumiendo m ≈ n)
2 ⋅ n ⋅ log2(n) << n
Operaciones AND y OR en Run-Length-Encoding  
Para dos bitmaps b1 y b2:  
Descomprimimos RLE (b1) y RLE (b2) en l´ınea (de izq. a der.).  
Comparamos bit a bit ejecutando AND u OR.

Se soluciona problema de espacio
¿c´omo insertamos/eliminamos una tupla en bitmap index?
Optimizaci´on de RLE para bitmap indexes
Dado que conocemos la cantidad de tuplas, entonces podemos omitir
la ´ultima secuencia de 0 en cada bitmap.
Esto nos permite
agregar un 1 en cualquier posici´on mayor o igual al ´ultimo 1.
Explotamos esta propiedad para hacer insert.
¿c´omo insertamos/eliminamos una tupla en bitmap index?
¿c´omo insertamos una tupla?
Para cada atributo c, buscamos el bitmap bc
v con v el nuevo valor.
Insertamos un nuevo 1 al final de bc
v .
¿c´omo eliminamos una tupla?
Marcamos con un tombstone su posici´on

¿c´omo buscamos el bitmap que necesitamos en Bc ?
● Soluci´on: B+-tree sobre c en Bc para encontrar bitmaps.
¿c´omo encontramos (al final) las tuplas del resultado?
● Soluci´on: ´ındice sobre la posici´on asignada a cada tupla.

Conclusiones sobre bitmap index
Muy ´util para :
consultas con filtros complejos.
columnas con baja cardinalidad.
datos con poco updates (e.g. OLAP).
Deficientes:
datos con updates frequentes.
Implementado por DB comerciales (Oracle)