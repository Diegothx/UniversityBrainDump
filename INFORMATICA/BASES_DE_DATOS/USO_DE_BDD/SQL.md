### Componentes del lenguaje:

- Data definition language (DDL).
	Ejemplo: CREATE, ALTER, etc...
- Data manipulation language (DML).
	Ejemplo: SELECT, INSERT, DELETE, etc...
- Data control language (DCL).
	Ejemplo: GRANT, REVOKE, etc..

### Sintaxis del lenguaje (SQL):

- Reglas que definen qué strings son válidos para usar en una consulta  SQL.
- Pueden variar según el dialecto específico de SQL que estés utilizando. 
- Reglas generales:
  - Uso adecuado de palabras clave como SELECT, FROM, WHERE, etc.
  - Sintaxis correcta para operadores como =, >, <, etc.
  - Uso adecuado de comillas simples (' ') o dobles (" ") para delimitar cadenas de texto.
  - Correcta utilización de funciones y expresiones SQL.
  Ejemplos:
  - SELECT * FROM tabla WHERE columna = 'valor';
  - INSERT INTO tabla (columna1, columna2) VALUES ('valor1', 'valor2');
  - UPDATE tabla SET columna = 'nuevo_valor' WHERE condicion;
  - SELECT FROM tables T1 AND T2 where b - 3 (sintaxis erronea)
  - SELECT a FROM x, z WHERE b = 3 (sintaxis correcta, semantica erronea)

### Semántica del lenguaje (SQL):

- Significado de las instrucciones y los datos utilizados en una consulta. 
- Esto incluye:
  - El significado de las palabras clave y los operadores, como SELECT, FROM, WHERE, etc.
  - Interpretación de los datos almacenados en la base de datos y cómo se relacionan entre sí.
  - Significado de las funciones y expresiones SQL utilizadas en una consulta.
  Ejemplos:
  - SELECT * FROM empleados WHERE salario > 50000;
    - Esta consulta selecciona todos los empleados cuyos salarios son mayores que 50000.
  - INSERT INTO clientes (nombre, edad) VALUES ('Juan', 30);
    - Esta instrucción inserta un nuevo cliente en la tabla de clientes con el nombre "Juan" y la edad de 30 años.
  - UPDATE productos SET precio = precio * 1.1 WHERE categoria = 'Electrónica';
    - Esta instrucción actualiza el precio de todos los productos en la categoría de electrónica aumentándolos en un 10%.
### Forma básica de consulta SQL:

```sql 
	SELECT <atributos> FROM <relaciones> WHERE <condiciones>
```

Ejemplos:

```sql 
	SELECT pName, pYear FROM Players WHERE pGoals ≥ 100
	SELECT P.pName AS Jugador, P.pYear AS A˜no FROM Players AS P WHERE pYear = 1990
	SELECT DISTINCT pName AS Nombre, mStadium AS Estadio FROM Players, Matches WHERE pId = mId
	SELECT pName, AVG(mGoals) FROM Players, Matches WHERE pId = mId GROUP BY pId, pName
```
### Consultas anidadas: 

Subconsulta embebida en WHERE, FROM o HAVING.

Ejemplos:

```sql
SELECT pName, pGoals
FROM Players
WHERE pGoals = ( SELECT MAX(pGoals)
				 FROM Players )
				 
SELECT DISTINCT pName, pYear
FROM Players, ( SELECT mId
				FROM Matches
				WHERE mGoals ≥ 3 )
WHERE pId = mId
```
### Consultas correlacionadas: 

Subconsulta embebida con referencia a la consulta externa.

Ejemplos:

```sql
SELECT pName, pYear
FROM Players AS P
WHERE 1 ≤ ( SELECT AVG(mGoals)
			FROM Matches AS M
			WHERE M.mId = P.pId )
```

### Desde SQL a [[ALGEBRA_RELACIONAL]]

Cómo evaluarían esta consulta Q?
**Players(pId, pName, pYear)**
**Matches(mId, mStadium, mDate)**
**Players Matches(pId, mId, goals)**

Plan lógico (= árbol de parsing) de Q:
- πpName,mStadium,goals
- &
- σpYear=1990
- Players
- &
- Matches Players Matches

El plan lógico será nuestro punto inicial para la evaluación de la consulta.

**SELECT pName, mStadium, goals
FROM Players AS P, Matches AS M, Players Matches AS PM
WHERE P.pId = PM.pId AND PM.mId = M.mId AND
P.pYear ≥ 1985**


**Idea (intuitiva)**
1. Combine las relaciones en el FROM con productos cruz: R1 × . . . × Rn
2. Aplique selección con la condición dada en el WHERE: σϕ( R1 × . . . × Rn )
3. Aplique proyección con las atributos dados en el SELECT: πatt1,...,attn ( σϕ( R1 × . . . × Rn ) )
