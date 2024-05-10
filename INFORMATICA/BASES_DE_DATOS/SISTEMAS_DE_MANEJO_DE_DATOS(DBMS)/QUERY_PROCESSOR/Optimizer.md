![[Pasted image 20240502221813.png]]
## Índice
- **Optimizador de consultas**
- **Plan físico**
- **Costo de un plan físico**
- **Implementación de operadores relacionales**

---

## Optimizador de consultas
- Se encarga de calcular y seleccionar el plan con el menor costo
- INPUT: Plan lógico L.
- OUTPUT: Plan físico F.

---

## Plan físico
- Árbol ordenado y etiquetado T similar a un plan lógico:
	label(n) es una implementación de un operador en Algebra Relacional
	para todo nodo interno n ∈ nodes(T )
- Ejemplo:
``` mathematica
							PROJ(pName,mStadium,goals)
										|
									HASH-JOIN
								/               \
						INDEX-SELpYear>1985    NL-JOIN
											  /       \
									PROJ(mld,mStadium) PM
											 |
											 M
```
- También cuenta con información extra, como:
	- Selección del access method para cada relación.
	- Detalles de implementación (Orden del input importa).
	- Detalles de la ejecución. Pipeline y materialización
- Ejemplo conversión plan lógico a físico:
```mathematica
          πpName,mStadium,goals              PROJ(pName,mStadium,goals)
		            |                                     |
			        ⋈              -->                HASH-JOIN
		          /   \                           /               \
		         ⋈     M                 INDEX-SELpYear>1985    NL-JOIN
		      /     \ 										   /       \
		σpYear>1985  PM  			            	PROJ(mld,mStadium) PM
		     | 				                          		 |
		     P   				        					 M    			
```


---

## Costo de un plan físico
- Múltiples parámetros a considerar
    - Acceso a disco (estándar).
    - Ciclos de CPU.
    - Uso de memoria.
    - Balance de la carga (computación en paralelo).
    - Comunicación.
- Cuando hablamos de optimización por tiempo (como ahora), normalmente estaremos enfocados solo en el Acceso a disco (I/O), ya que este tiende a ser el cuello de botella
- Estimamos el costo según acceso al disco de cada operador, para esto usamos las estadísticas del catalogo.
- El costo del plan es la suma del costo según acceso al disco de cada operador
- “Performance del DBMS esta fuertemente ligado a una buena estimación del costo de sus planes físicos.”
- Parámetros de interés:
    - cost(R): costo (en I/O) para computar R.
    - pages(R): cantidad de páginas necesarias para almacenar R.
    - ∣R∣: cantidad de tuplas/récords en R.
    - rsize(R): tamaño de una tupla/récord (promedio) en R.
    - distinct(R): cantidad de elementos distintos en R.
    - distincta(R): cantidad de elementos distintos en el campo R.a.
    - ∣page∣: tamaño/espacio de una página∗
    - selectividad.
        - selp (R): fracción de tuplas/récords en R que satisfacen p.
        - donde p es una combinación booleana (∧, ∨) de términos:
            - attributo1 op attributo2
            - attributo op constante
            - con op ∈ {=, ≤, ≥, <, >}.
        - Definición
            - 0 ≤ selp (R) = ∣σp (R)/∣R∣ ≤ 1
- Ejemplo
    - Los parámetros de sorting de R (o τ (R)) son:
        - cost(τ (R)) = cost(R) + 2 ⋅ pages(R)
        - pages(τ (R)) = pages(R)
        - ∣τ (R)∣ = ∣R∣
        - rsize(τ (R)) = rsize(R)
    - Operadores y tamaño del input
- Suposición para todos los operadores físicos siguientes:
    - Se asume que para todos los operadores unarios ∗(R) o binarios R ∗ S, ambas relaciones R y S son de tamaño mayor a la disponible en el buffer.
    - Si R o S pueden ser almacenadas en el buffer (memoria), entonces:
        - cost(∗(R)) = cost(R)
        - cost(R ∗ S) = cost(R) + cost(S)

---

## Implementación de operadores relacionales
### Definición
- Algoritmos usados para ejecutar los operadores físicos
- Se pueden dividir en
	1. Iteración.
	2. Hashing.
	3. Sorting.
	4. Índices.
	5. Otros.
- No existe una implementación que sea la mejor para todos los casos.
- El mejor algoritmo dependerá de la distribución y tamaño de los datos.
- Cada variante aprovecha propiedades físicas de los datas:
	- presencia o ausencia de índices.
	- Orden del input.
	- Tamaño del input.
	- Cantidad de elementos distintos.
	- Espacio disponible en memoria.
### Estructura
Cada implementación opera con esta interfaz
1. open()
	- inicializa el estado del operador.
	- Ajusta parámetros (ej. condición de selección).
2. next()
	- Procesa el input y responde la siguiente tupla.
3. close()
	- Limpia elementos temporales.



## Operadores físicos relacionales
*Suposición para todos los operadores físicos siguientes:
Se asume que para todos los operadores unarios ∗(R) o binarios R ∗ S, ambas relaciones R y S son de tamaño mayor a la disponible en el buffer.*
### Selección (σ)
σp (R), donde p es una combinación booleana (∧, ∨) de términos:
attributo1 op attributo2|constante
#### Selección (σp ): sin índices
1. Leer todas las tuplas, una a una.
2. Retornar la tupla si satisface el predicado.
##### Algoritmo
```python
input: Predicado p y relación (e.g. operador) R.

										next()
open()                                      t ∶= R.next()
	R.open()                                while t ≠ NULL do                  
close()                                         if p(t) = true then
	R.close()                                       return t
		                                        t ∶= R.next()
										    return NULL
```
##### Costos  y parámetros:
cost(σp (R)) = cost(R)
pages(σp (R)) = selp (R) ⋅ pages(R)
∣σp (R)∣ = selp (R) ⋅ ∣R∣
#### Selección (σp ): primary index, p ∶= A = v
1. Buscamos la primera tupla que satisface A = v.
2. Retornar las siguientes tuplas haciendo next del índice.
##### Algoritmo
```python
input: Predicado p ∶= A = v y relación R y índice I.

open()                                      next()
	I.open()                                    t ∶= I.next()
	I.search(A = v)                             if t =/= NULL then				
close()                                             return t
	I.close()                                   return NULL    
next()
```
##### Costos y parámetros:
cost(σp (R)) = cost(I)[3] + selp (R) ⋅ pages(R)
pages(σp (R)) = selp (R) ⋅ pages(R)
∣σp (R)∣ = selp (R) ⋅ ∣R∣
*cost(I) depende del tipo de índice, pero en general es aproximadamente 3*
#### Selección (σp ): secondary index, p ∶= A = v
1. Buscamos la primera tupla que satisface A = v.
2. Por cada data entry k∗ que satisface A = v:
    - Buscamos la página en k∗.RID.
    - Retornamos la tupla con RID igual a k∗.RID.
##### Algoritmo
```python
input: Predicado p ∶= A = v y relación R y índice I.

open()                                      next()
	I.open()                                    k∗ ∶= I.next()
	I.search(A = v)                             if k∗ =/= NULL then				
close()                                             return R.get(k∗.RID)
	I.close()                                   return NULL    
next()
```
##### Costos y parámetros:
cost(σp (R)) = cost(I)[3] + selp (R) ⋅ ∣R∣
pages(σp (R)) = selp (R) ⋅ pages(R)
∣σp (R)∣ = selp (R) ⋅ ∣R∣
*Notar que selp (R) ⋅ pages(R) << selp (R) ⋅ ∣R∣, por lo cual es peor que el anterior, a menos que selp(R) ->0, ahí la diferencia es mínima*
#### Selección caso p ∶= A ≤ v
Mismos costes/parámetros, pero solamente si índice soporta range queries (B+-tree). Recordar que Hash index no soporta range queries y costo/parámetros es el mismo que hacer una selección sin índice.
#### Selección (σp ): caso general
Consideramos tres casos:
1. Predicados conjuntivos.
 ![[Pasted image 20240504121515.png]]
3. Predicados disyuntivos.
![[Pasted image 20240504121523.png]]
5. Predicados booleanos sin restricciones.

#### Selección (σp ): predicados conjuntivos
Varias posibilidades:
- Hacemos un scan sobre toda la relación y filtramos tuplas.
- Usamos un índice multidimensional (ej: bitmap index).
- Escogemos la conjunción pi ∶= ( Ai opi vi ) que tenga un índice sobre Ai y con mayor selectividad (menor selpi (R)). Las tuplas/records retornadas por el índice para pi las filtramos con p.
- Tratamos de usar varios índices a la vez si la selectividad de cada predicado es baja y todos los índices son unclustered/secundarios.

#### Selección (σp ): predicados disyuntivos
Dos posibilidades:
1. Si al menos un término NO hace match ningún índice, entonces hacemos filter/scan de toda la relación.
2. Si cada término hace match con al menos un índice, entonces evaluamos cada término por separado, hacemos la unión de todos los resultados y eliminamos duplicados.

#### Selección (σp ): predicados booleanos sin restricciones
Combinación de las anteriores.

#### Definición de Selección (σ)
Selección: σp (R).
p es una combinación booleana (∧, ∨) de términos:
attributo1 op attributo2 constante

#### Selección (σp ): sin índices
1. Leer todas las tuplas, una a una.
2. Retornar la tupla si satisface el predicado.

#### Selección (σp ): primary index, p ∶= A = v
1. Buscamos la primera tupla que satisface A = v.
2. Retornar las siguientes tuplas haciendo next del índice.

#### Selección (σp ): secondary index, p ∶= A = v
1. Buscamos la primera tupla que satisface A = v.
2. Por cada data entry k∗ que satisface A = v:
    - Buscamos la página en k∗.RID.
    - Retornamos la tupla con RID igual a k∗.RID.

#### Selección (σp ): caso general
Consideramos tres casos:
1. Predicados conjuntivos.
2. Predicados disyuntivos.
3. Predicados booleanos sin restricciones.

#### Selección (σp ): predicados conjuntivos
Varias posibilidades:
- Hacemos un scan sobre toda la relación y filtramos tuplas.
- Usamos un índice multidimensional (ej: bitmap index).
- Escogemos la conjunción pi ∶= ( Ai opi vi ) que tenga un índice sobre Ai y con mayor selectividad (menor selpi (R)). Las tuplas/records retornadas por el índice para pi las filtramos con p.
- Tratamos de usar varios índices a la vez si la selectividad de cada predicado es baja y todos los índices son unclustered/secundarios.

#### Selección (σp ): predicados disyuntivos
Dos posibilidades:
1. Si al menos un término NO hace match ningún índice, entonces hacemos filter/scan de toda la relación.
2. Si cada término hace match con al menos un índice, entonces evaluamos cada término por separado, hacemos la unión de todos los resultados y eliminamos duplicados.

#### Selección (σp ): predicados booleanos sin restricciones
Combinación de las anteriores. La estrategia consiste en
1. Buscar un termino disyuntivo que nos obligue a realizar un filter/scan.
2. Buscar un termino conjuntivo que tenga mayor selectividad
### Proyección (πL)
Proyección πL es muy sencillo (especial para pipeline):
1. Leemos las tuplas/records de R, una a una.
2. Proyectamos en los atributos L.
```python
input: Lista de atributos L y operador R.

open()									next()
	R.open()                                t ∶= R.next()
close()                                     if t ≠ NULL then                  
	R.close()                                   return t.project(L)
                                           return NULL
```
#### Costos y parámetros:
cost(πL(R)) = cost(R)
pages(πL(R)) = rsize(πL(R))/rsize(R) ⋅ pages(R)
∣πL(R)∣ = ∣R∣
rsize(πL(R)) = ∑att∈L E(∣πatt (R)∣)
*Esta proyección no considera eliminacion de duplicados.*
### Sorting
- [[External_Merge-sort]].
- [[B+-TREES]].
	Si un B+-tree hace match para una tarea de sorting, entonces considerar usar el índice.
		Clustered index : el mejor de los casos.
		Unclustered index
### Eliminacion de duplicados
Dos implementaciones:
#### Sorting
1. Hacemos external merge-sort de las tuplas/records.
2. En el último paso, filtramos según la última tupla leida
##### Algoritmo
```python
input: Operador R.

open()      							next()
	R′∶= R.merge-sort(R)                    t′ ∶= R′.next()
	R′.open()                                while t′ ≠ NULL do              
	t ∶= NULL                                   if t ≠ t′ then
close()                                             t ∶= t′
	R′.close()                                      return t
		                                        t′ ∶= R′.next()
										    return NULL
```
##### Costo y parámetros de δ(R) con sorting:
cost(δ(R)) = cost(R) + 2 ⋅ pages(R)
pages(δ(R)) = distinct(R) ⋅ rsize(R)
∣page∣
∣δ(R)∣ = distinct(R)
rsize(τ (R)) = rsize(R)

#### Hashing
Consta de dos partes:
1. Fase de particion.
	- Cada página p del operador R se lee a memoria.
	- Por cada t ∈ p se computa h(t) y se envía al bucket Bucketh(t).
	- Si un bucket esta completo, se vacía y materializa en disco.
	- Cada bucket construye una secuencia de overflow pages en disco.
 ![[Pasted image 20240504183813.png]]
2. Fase de eliminación de duplicados
	- Cada bucket b se lee a memoria.
	- Se eliminan los duplicados con un algoritmo de memoria interna.
	- Si bucket b no cabe en memoria, se itera la fase de partici´on con b.
Costo y par´ametros de δ(R) con hashing:
	cost(δ(R)) = cost(R) + 2 ⋅ pages(R)
	pages(δ(R)) = distinct(R) ⋅ rsize(R)
	∣page∣
	∣δ(R)∣ = distinct(R)
	rsize(τ (R)) = rsize(R)
¿qu´e ventajas tiene la versi´on basada en sorting?
	● Los resultados quedan ordenado.
	● No se ve afectado por datos sesgados.
	¿qu´e ventajas tiene la versi´on basada en hashing?
	● Posibilidad de usar m´as buffer.
### Group By
Dos posibles implementaciones:
1. Basado en sorting.
2. Basado en hashing.
Los mismos algoritmos para duplicados pero con agregaci´on!
### Unión
Dos alternativas:
1. Si R y S  est´an ordenados: leemos y imprimimos.
2. Si R y S no est´an ordenados: leemos, ordenamos y imprimimos.
	● Hacemos merge de R y S.
	● No aumenta el costo I/O del operador.
   Costo y parámetros de R ∪ S:
	cost(R ∪ S) = cost(R) + cost(S)
	pages(R ∪ S) = pages(R) + pages(S)
	∣R ∪ S∣ = ∣R∣ + ∣S∣
	rsize(R ∪ S) = rsize(R)
#### Unión sin duplicados
Mismas alternativas que para eliminación de duplicados:
1. Basado en sorting.
● Ordenamos ambas relaciones con sorting externo.
● Hacemos merge de ambas relaciones.
2. Basado en hashing.
● Particionamos R y S con una funci´on de hash.
● Eliminamos duplicados en cada partición.
Mismo costo que para eliminación de duplicados
### Agregación
- Uso de índices
### Join
#### Tipos de Join
Producto cruz: R1 × R2
Equi-join: R1 &ϕ R2 = σϕ(R1 × R2)
	● ϕ solo contiene igualdades.
Natural-join: R1 & R2 = σϕ(R1 × R2)
	● ϕ = ⋀a∈att(R1)∩att(R2) R1.a = R2.a.
p-join: R1 ⋈p R2 = σp (R1 × R2)
	● p es una combinación booleana (∧, ∨) de términos:
		attributo1 op attributo2
		attributo op constante
	con op ∈ {=, ≤, ≥, <, >}.
#### Joins (⋈p): Nested loops join
##### **Algoritmo**
```python
- input: Operadores R y S, y un predicado p.
- open()
  - R.open()
  - S.open()
  - r := R.next()

- close()
  - R.close()
  - S.close()

- next()
  - while r ≠ NULL do
    - s := S.next()
    - if s = NULL then
      - S.close()
      - r := R.next()
      - S.open()
    - else if (r, s) satisfacen p then
      - return (r, s)
  - return NULL
```


##### **Costo y parámetros de nested loops join:**
- cost(R ⋈p S) = cost(R) + |R| * cost(S) 
- pages(R ⋈p S) = selp(R × S) * pages(R) * pages(S) 
- |R ⋈p S| = selp(R × S) * |R| * |S| 
- rsize(R ⋈p S) = rsize(R) + rsize(S)`
##### Ejemplo:
- R y S son tablas de 16MB.
- Cada página tiene 8KB.
- Tuplas son de 300 Bytes. Esto significa:
- Cada relación tiene 2048 páginas.
- Cada relación tiene aproximadamente 55,000 tuplas. Si cada I/O toma aproximadamente 0.1ms (mínimo):
- Tardaría aproximadamente 3.1 horas.

#### Joins (⋈p): Block nested loops join
![[Pasted image 20240504204843.png]]
##### **Algoritmo**
```python
- input: Operadores R y S, un predicado p, y un Buffer con B + 1 páginas.
- open()
  - R.open()
  - fillBuffer()

- fillBuffer()
  - Buff ∶= ∅
  - r ∶= R.next()
  - while r ≠ NULL do
    - Buff := Buff ∪ {r}
    - if Buff.isFull() then
      - break
    - r := R.next()
  - S.open()
  - s := S.next()

- next()
  - while Buff ≠ ∅ do
	- while s ≠ NULL do
	  - r ∶= Buffer .next()
	  - if r = NULL then
		- Buff .reset()
		- s ∶= S.next()
	  - else if (r , s) ⊧ p then
	  - return (r , s)
	- fillBuffer()
  - return NULL

- close()
  - R.close()
  - S.close()
```

##### **Costo de Block nested loops join:**
- cost(R ⋈p S) = cost(R) +∣R∣ / (∣page∣ ⋅B) * cost(S) o cost(R ⋈p S) = cost(R) + page(R)/B * cost(S)
- Ojo, cost(R &p S) ≠ cost(S &p R)
- Regla: la relacion mas pequena tiene que ir la exterior
##### Ejemplos:
1. 
- R y S son tablas de 16MB.
- Cada página tiene 8KB.
- Un buffer de 1MB. Esto significa:
- Cada relación tiene 2048 páginas.
- Tenemos 128 páginas de buffer. Si cada I/O toma aproximadamente 0.1ms (mínimo):
- Tardaría aproximadamente 3.4 segundos.
2. 
- R y S son tablas de 250GB.
- Cada pagina tiene 8KB.
- Un buffer de 32GB.
- Esto significa que:
- Cada relacion tiene 32.768.000 paginas.
- Tenemos 4.194.304 paginas de buffer.
- Si consideramos que cada I/O toma ≈ 0.1ms (mınimo):
- 8 horas!!!

#### Joins (⋈p): Index nested loops join

##### **Algoritmo**
```python
- input: Operadores R y S, y un predicado p. 
- open()   
  - R.open()   
  - Index(S).open() 
  - r := R.next() 
  - I ∶= S.index(r ,p)

- close()
  - R.close()
  - Index(S).close()

- next()
  - while r ≠ NULL do   
	- s := I.next()
	- if s = NULL then     
	  - r := R.next()     
	  - I ∶= S.index(r ,p)
	- else     
	  - return (r, s) 
  - return NULL`
```

##### **Costo de Index nested loops join:**
Clustered Index: cost(R ⋈p S) = cost(R) + |R| * ⌈pages(S) * selR⋈p(S)⌉
Unclustered Index: cost(R ⋈p S) = cost(R) + |R| * ⌈|S| * selR⋈p(S)⌉
*En este costo, NO estamos considerando el costo del ´ındice*
##### Ejemplo:
- R y S son tablas de 16MB.
- Cada página tiene 8KB.
- Tuplas de 300 Bytes.
- S tiene un índice agrupado y la selectividad es 0.0001. Si cada I/O toma aproximadamente 0.1ms (mínimo):
- Tardaría aproximadamente 5.7 segundos.
##### Reflexiones sobre index nested loops join
- Utilizar INLJ, cuando:   
	- Selectividad es muy alta (selR⋈p(S) ≈ 0).   
	- Relación (consulta) R tiene pocas tuplas.
#### Joins (⋈): Sort-merge join

##### **Algoritmo**
```python
- input: Operadores R y S, un predicado A = B. 
- open()   
  - R′ ∶= merge-sort(R)
  - S′ ∶= merge-sort(S)
  - r ∶= R′.next()
  - s ∶= S′.next()
  - fillBuffer()

- next()
  - while r ≠ NULL do
	- t ∶= Buffer .next()
	- if t = NULL then
	  - r ′ ∶= r
	  - r ∶= R′.next()
	  - if r (A) = r ′(A) then
	    - Buff .reset()
	  - else
  	    - fillBuffer()
	- else
	  - return (r , s)
  - return NULL

- fillBuffer()
  - while r (A) ≠ s(B) do
	- if r < s then
	  - r ∶= R′.next()
    - else
	  - s ∶= S′.next()
  - Buff ∶= ∅
  - while r (A) = s(B) do
	- Buff ∶= Buff ∪{s}
	- s ∶= S′.next()
	- 
- close()
  - R′.close()
  - S′.close()
```

##### **Costo de Sort-merge join:**
Formula general: cost(R (⋈):A=B S) = cost(R) + cost(S) + 2 ⋅ (pages(R) + pages(S))
Relaciones: cost(R (⋈):A=B S) = 3 ⋅ (pages(R) + pages(S))
##### Ejemplos:
1. 
- R y S son tablas de 16MB.
- Cada página tiene 8KB.
- Cada relación tiene 2048 páginas. Si cada I/O toma aproximadamente 0.1ms (mínimo):
- Tardaría aproximadamente 1.2 segundos.
2. 
- R y S son tablas de 250GB.
- Cada página tiene 8KB.
- Cada relación tiene 32,768,000 páginas. Si cada I/O toma aproximadamente 0.1ms (mínimo):
- Tardaría aproximadamente 5.4 horas.
##### Reflexiones sobre Sort-merge join

- Operador físico preferido para &A=B (sin suposiciones). 
- Más rápido aún si ambas relaciones ya están ordenadas. 
- ¿Cuándo podría suceder que ambas relaciones estén ordenadas?`
##### Sort-merge join + B+-tree
Costo: cost(R ⋈A=B S) = pages(R) + pages(S)
Ejemplo:
- R y S son tablas de 250GB.
- Cada p´agina tiene 8KB.
- R y S tienen un clustered B+-tree en A y B.
- Si consideramos que cada I/O toma ≈ 0.1ms (m´ınimo):
- 1, 8 horas!!!
#### Joins (⋈): Hash join
Misma idea que eliminaci´on de duplicados:
1. Fase de partici´on.
	● Cada p´agina p del operador R se lee a memoria.
	● Por cada t ∈ p se computa h(t) y se env´ıa al bucket Rh(t).
	● Cada p´agina p del operador S se lee a memoria.
	● Por cada t ∈ p se computa h(t) y se env´ıa al bucket Sh(t).
	(si un bucket esta completo, se vac´ıa y materializa en disco)
 ![[Pasted image 20240504221433.png]]
 2. Fase de join de inter-particiones.
	● Join Ri &p Si entre los bucket Ri y Si , recursivamente.
	● (Idealmente) Ri o Si sea almacenado completamente en el buffer.
	Costo de Hash join:
	Formula general
	cost(R &A=B S) = cost(R) + cost(S) + 2 ⋅ (pages(R) + pages(S))
	Relaciones
	cost(R &A=B S) = 3 ⋅ (pages(R) + pages(S))
	Mismo costo que Sort merge join!
#### Resumen de costos para algoritmos de Join
- Block nested loops join:
  - pages(R) + pages(R) * pages(S) / B
- Index nested loops join:
  - pages(R) + |R| * ⌈pages(S) * selR⋈p(S)⌉
- Sort-merge join (funciona con cualquier distribución de datos y el resultado es ordenado):
  - 3 * (pages(R) + pages(S))
- Hash join (oernute sacarki mayor provecho al buffer):
  - 3 * (pages(R) + pages(S))



