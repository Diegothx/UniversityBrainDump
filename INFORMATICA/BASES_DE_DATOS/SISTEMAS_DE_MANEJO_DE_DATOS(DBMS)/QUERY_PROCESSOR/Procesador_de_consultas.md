##
![[Pasted image 20240502190605.png]]

---

## Input: [[SQL]] query
- Ejemplo: 
``` sql
SELECT orders.order_id, orders.order_date, customers.customer_name
FROM orders
INNER JOIN customers ON orders.customer_id = customers.customer_id
WHERE customers.city = 'New York';
```
## Paso 1: [[Parser]]
- Valida la sintaxis de la consulta. 
- Convierte consulta en formato interno (parse tree). 
- Optimizaciones menores (numéricas, etc). 
## Paso 2: [[Preprocessor]]
- Hace chequeos usando la metadata de la DB.
## Paso 3: [Reescritura de consulta](Query_Rewrite)

- “Desanidación” de la consulta (flattening). 
- Reescribe consulta aplicando reglas de álgebra relacional. 
- Crea un set de planes lógicos para ser optimizados. 
## Paso 4: [Optimizador](Optimizer)

- Encuentra el plan físico más eficiente para ejecutar la consulta. 
- El optimizador debe considerar: 
- Tamaño de cada relación y distribución de sus datos. 
- Distintos accesos a las relaciones (índices, etc). 
- Distintos planes lógicos para la misma consulta. 
- Distintos algoritmos para un mismo operador relacional. 
## Paso 5: [Ejecución](Executor)
- Ejecuta el plan óptimo encontrado por Optimizador (pipelining o materialización).
## Output: Tuplas buscadas
- Ejemplo: 
```sql
| order_id | order_date | customer_name   | 
|----------|------------|-----------------| 
| 1        | 2024-04-01 | John Doe        | 
| 2        | 2024-04-05 | Jane Smith      | 
| 3        | 2024-04-10 | Michael Johnson |
```