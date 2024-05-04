![[Pasted image 20240503135501.png]]
## Indice
- **Ejecución**
- **Pipeline**
- **Materialización**

---
## Ejecución
Se ejecuta el plan físico, idealmente con un pipeline pero en caso de tener una subconsulta llamada reiteradas veces o costo de almacenamiento menor a recalcular la consulta se usa materialización como checkpoint de la data.

---

## Pipeline
- Ejecución en serie del plan físico
- Cada operador genera una tupla a la vez.
- Cada operador retorna los resultados a su operador padre sin guardar resultados intermedios
- Beneficios:
	- Simplifica sincronización.
	- Evita lectura/escritura intermedia de los resultados.
	- Posible paralelización de operadores.
- No permite bloqueo de operadores. Lo cual pasa cuando un operador tiene que esperar a terminar todo el proceso, como en el caso de los:
	- external-sorting.
	- joins.
	- group-by y eliminación de duplicados.

---

## Materialización
- Alternativa a Pipeline cuando este no es suficiente
- Almacena el resultado de la subconsulta en disco.
- Solo necesario si la subconsulta es requerida reiteradas veces
![[Pasted image 20240502190458.png]]
