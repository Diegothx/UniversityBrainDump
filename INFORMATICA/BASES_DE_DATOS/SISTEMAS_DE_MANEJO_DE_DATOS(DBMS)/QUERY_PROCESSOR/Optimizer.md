![[Pasted image 20240502221813.png]]
## Índice
- **Optimizador de consultas**
- **Implementación de operadores relacionales**
- **Plan físico**
- **Costo de un plan físico**

---

## Optimizador de consultas
- Se encarga de calcular y seleccionar el plan con el menor costo
- INPUT: Plan lógico L.
- OUTPUT: Plan físico F.

---

## Implementación de operadores relacionales 
- Diversas implementaciones por cada operador relacional
- Algunas implementaciones son:
	- Selection:
		Index/No Index.
		Sorted/unsorted.
		Múltiples condiciones.
	- Projeccion:
		Sorting.
		Hashing.
	- Sorting:
		[[External_Merge-sort]].
		[[B+TREES]].
			Si un B+-tree hace match para una tarea de sorting, entonces considerar usar el índice.
				Clustered index : el mejor de los casos.
				Unclustered index
	- Join:
		Nested Loops Join.
		Sort-Merge Join.
		Hash Join.
	- Unión / Diferencia:
		Sorting.
		Hashing.
	- Agregación:
		Uso de índices
- No existe una implementación que sea la mejor para todos los casos.
- El mejor algoritmo dependerá de la distribución y tamaño de los datos.
- Cada variante aprovecha propiedades físicas de los datas:
	- presencia o ausencia de índices.
	- Orden del input.
	- Tamaño del input.
	- Cantidad de elementos distintos.
	- Espacio disponible en memoria.
- Cada operador implementa esta interfaz:
	1. open()
		- inicializa el estado del operador.
		- Ajusta parámetros (ej. condición de selección).
	2. next()
		- Procesa el input y responde la siguiente tupla.
	1. close()
		- Limpia elementos temporales.

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
	cost(R): costo (en I/O) para computar R.
	pages(R): cantidad de páginas necesarias para almacenar R.
	∣R∣: cantidad de tuplas/récords en R.
	rsize(R): tamaño de una tupla/récord (promedio) en R.
	distinct(R): cantidad de elementos distintos en R.
	distincta(R): cantidad de elementos distintos en el campo R.a.
	∣page∣: tamaño/espacio de una página∗
	selectividad.
		selp (R): fracción de tuplas/récords en R que satisfacen p.
		donde p es una combinación booleana (∧, ∨) de términos:
		attributo1 op attributo2
		attributo op constante
		con op ∈ {=, ≤, ≥, <, >}.
		Definición
		0 ≤ selp (R) = ∣σp (R)/∣R∣ ≤ 1
- Ejemplo
	Los parámetros de sorting de R (o τ (R)) son:
	cost(τ (R)) = cost(R) + 2 ⋅ pages(R)
	pages(τ (R)) = pages(R)
	∣τ (R)∣ = ∣R∣
	rsize(τ (R)) = rsize(R)
	Operadores y tamaño del input
- Suposición para todos los operadores físicos siguientes:
	Se asume que para todos los operadores unarios ∗(R) o binarios
	R ∗ S, ambas relaciones R y S son de tamaño mayor a la disponible
	en el buffer.
	Si R o S pueden ser almacenadas en el buffer (memoria), entonces:
	cost(∗(R)) = cost(R)
	cost(R ∗ S) = cost(R) + cost(S)