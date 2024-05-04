![[Pasted image 20240502185151.png]]
## Índice
- **Parser**
- **Análisis léxico (Lexer)**
- **Análisis sintáctico (Parser)**
- **Árbol de derivación**
- **Árbol de parsing**

---
## Parser
- Se encarga de revisar la sintaxis de la consulta
- INPUT: Consulta Q y gramática GSQL (SQL).
- OUTPUT: Árbol de parsing T de Q:
	-  Lexer (o análisis léxico): lee y convierte Q en una secuencia de tokens: Q' = t1 . . . tn
	2. Parser (o análisis sintáctico): convierte Q' en un árbol de parsing T de GSQL para Q.

---

## Análisis léxico (Lexer)
- Lee una consulta de izquierda a derecha y la divide en una serie de `tokens` (subcadena válida dentro de la consulta).
- Los tokens se componen de:
	- Tipo: Constante , tabla, ...
	- Valor: El valor de la subcadena.
- Tipos de tokens en SQL:
	- integer (constante): 2, 345, 495, . . .
	- string (constante): ’pedro’, ’casa’, . . .
	- palabras clave: SELECT, FROM, . . .
	- identificadores: Jugadores, nombre, . . .
	- delimitadores (char): delimitadores como ’,’, ’=’, ’+’, . . .
- Ejemplo:
```sql
	select a from x, z where b = 3
	| Tipo          | Valor  |
	| ------------- | ------ |
	| keyword       | select |
	| identificador | a      |
	| keyword       | from   |
	| identificador | x      |
	| delimitador   | ,      |
	| identificador | z      |
	| keyword       | where  |
	| identificador | b      |
	| delimitador   | =      |
	| integer       | 3      |
```
- Algoritmo Lexer: *Próximamente en lenguajes regulares y autómatas finitos*

---

## Análisis sintáctico (Parser)

- Revisa la gramática del lenguaje (Conjunto de reglas que describen la combinaciones de secuencias de tokens valida en el lenguaje)
- Procesa la secuencia de tokens, uno a uno, haciendo el analisis sintatico verificando que se cumpla la gramatica en cada uno
- Ejemplo: Gramática para definir predicados (WHERE):
```sql
	<Constant> := string ∗ ∣ int∗
	<Field> := id∗
	<Expression> := <Field> ∣ <Constant>
	<Term> := <Expression> =∗ <Expression>
	<Predicate> := <Term> ∣ <Predicate> AND∗ <Predicate>
```
Los términos con (⋅)∗ son tokens.
- Algoritmo Parser : *lenguajes libres de contexto y automatas con stack*
### Gramática
- Definición: Una gramática G ( o gramática libre de contexto) es un par: G = (V , T , P, S) donde
	- V es un conjunto de variables (ej. <Constant/>)
	- T es un conjunto de tokens
	-  S ∈ V es la variable inicial
	- P es un conjunto de reglas de la forma:
		<var/> := α
	donde <var/> ∈ V y α es un string en V ∪ T .
- Ejemplo
```sql
	<Constant> := string ∗ ∣ int∗
	<Field> := id∗
	<Expression> := <Field> ∣ <Constant>
	<Term> := <Expression> =∗ <Expression>
	<Predicate> := <Term> ∣ <Predicate> AND∗ <Predicate>
	V = {<Constant>, <Field>, <Expression>, . . .}
	T = {Strings, Ints, =, AND}
	S = <Predicate>
	P = conjunto de reglas
```
- En estricto rigor, string ∗ es la regla generando todos los strings, . . .
- La gramática de un lenguaje puede ser muy compleja. Por ejemplo, ver la[ gramática de SQL-2003](https://ronsavage.github.io/SQL/sql-2003-2.bnf.html)

---

## Árbol de derivación
- Un árbol de derivación G = (V , T , P, S) es un árbol ordenado y rotulado T con etiquetas en V ∪ T tal que:
	label(h) ∈ T para todo nodo hoja h ∈ nodes(T),
	label(n) ∈ V para todo nodo interno n ∈ nodes(T),
	label(root(T)) = S,
	si n1, . . . , nk son los hijos de n ∈ nodes(T ), entonces:
		label(n) := label(n1) label(n2) ⋯ label(nk)
		es una regla en P
- Ejemplo:
  ![[Pasted image 20240502200144.png]]

---

## Árbol de parsing
- Un árbol de parsing de G = (V , T , P, S) para Q es un árbol de derivación T de G tal que si h1, . . . , hn son las hojas de T (en pre- o post-order) entonces:
	Q' = label(h1) ⋅ label(h2) ⋅ . . . ⋅ label(hn)
- Ejemplo
```sql
nombre = ’Cristian’ AND edad = 31
<Constant> := string ∗ ∣ int∗
<Field> := id∗
<Expression> := <Field> ∣ <Constant>
<Term> := <Expression> =∗ <Expression>
<Predicate> := <Term> ∣ <Predicate> AND∗ <Predicate>
```

![[Pasted image 20240502185620.png]]
- El árbol de parsing le da “estructura” a una oración.
- Árbol de parsing es nuestro punto de partida para la optimización
