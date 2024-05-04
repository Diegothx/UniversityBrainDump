![[Pasted image 20240502200832.png]]
## Índice
- **Preprocesamiento de consultas**
- **Metadatos**
- **Catálogo del sistema**

---

## Preprocesamiento de consultas
- Antes de la reescritura de consultas es necesario:  
	1. Chequear que el uso de las tablas es correcto.
	2. Chequear y resolver el uso de atributos.
	3. Chequear que los tipos son correctos.
	4. Calcular expresiones simples.
	5. Preprocesar el uso de vistas.
- Para esto, usamos el catalogo del sistema.

---

## Metadatos 
- Información de la DB, fuera de su contenido.
- Se pueden dividir en 4 categorías:
	1. Información sobre las relaciones: Por cada relación se almacenan:
		- Nombre de la relación.
		- Dirección de almacenamiento.
		- Restricciones de integridad.
		- Para cada campo/atributo:
			- Nombre.
			- Tipo de dato.
			- Tamaño.
			- Detalles de almacenamiento
		- Etc...
	2. Estadísticas del contenido: Por cada relación se almacenan:
		- Numero de paginas.
		- Número de tuplas/récords.
		- Tamaño (promedio) de una tupla.
		- Para cada campo/atributo:
			- Cantidad de valores distintos.
			- Tamaño (promedio).
			- Distribución de los datos (histogramas).
			- Valores máximos y mínimos.
		-  Etc...
	3. Información sobre los índices: Por cada índice de una relación se almacena:
		- Nombre del índice.
		- Campo indexado.
		- Tipo de índice.
		- Tamaño del índice.
		- Altura (en caso árbol).
		- Numero de overflow pages.
		- Costo promedio del índice (en bloques).
		- Etc...
	4. Información sobre las vistas: Por cada vista se almacena:
		- Nombre.
		- Definición.
		- Materialización (si existe).
		- Etc...
- Estadísticas del contenido e Información sobre los índices son importantes para la optimización.

---

## Catalogo del sistema
- Encargado de almacenar los metadatos en tablas (relaciones) del mismo sistema.
- Es traído a la memoria al iniciar el sistema.
- Puede ser actualizado según distintos protocolos:
	- Consulta a consulta.
	- Periódicamente.
	- Recalcular metadatos (siempre que se estime necesario).