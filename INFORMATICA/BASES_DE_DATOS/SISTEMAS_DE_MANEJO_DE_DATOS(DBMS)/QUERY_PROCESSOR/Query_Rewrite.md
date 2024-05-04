![[Pasted image 20240502214408.png]]
## Índice
- **Query Rewrite**
- **Plan Lógico**
- **Conversión Parse tree a Árbol lógico**
- **Set planes lógicos para ser optimizado**

---

## Query Rewrite
1. Convierte el árbol de parsing en un plan lógico.
2. Reescribe consulta aplicando reglas de algebra relacional.
3. Crea un set de planes lógicos prometedores para ser optimizado

---

## Plan lógico
- Árbol ordenado y etiquetado T tal que, para todo nodo hoja h ∈ nodes(T) y nodo interno n ∈ nodes(T):
	label(h) es una relación,
	label(n) es un operador de algebra relacional y
	∣ children(n)∣ = arity(label(n))
- Ejemplo: `π(x, y) ⋈ (⋈(σ(z > 80) S) ⋈ R)
`
``` mathematica
formula:
	π(x, y) ⋈ (⋈(σ(z > 80) S) ⋈ R)
	
Plan lógico:
          πx,y
            |
            ⋈
          /   \
         ⋈     R
       /   \
   σz>80   S
      |
      T      
```

---

## Conversión Parse tree a Árbol lógico
### Pasos generales
Teniendo la estructura básica de una consulta SQL:
```sql
	SELECT <SelList>
	FROM <FromList>
	WHERE <Condition>
```

1. Combinamos las relaciones en el <FromList/> con productos cruz: R1 × . . . × Rn
2. Aplicamos selección con la condición C dada en el <Condition/>: σ<Condition/>( R1 × . . . × Rn )
3. Aplicamos proyección con las atributos dados en el <SelList/>: π<SelList/> ( σ<Condition/>( R1 × . . . × Rn ) )
### Ejemplo:
 ```sql
	SELECT pName, mStadium, goals
	FROM Players AS P, Matches AS M, Players Matches AS PM
	WHERE P.pId = PM.pId AND PM.mId = M.mId AND
	P.pYear ≥ 1985
```

```mathematica
formula:
	π(pName, mStadium, goals)(σ(P.pId=PM.pId ∧ PM.mId=M.mId ∧ P.pYear ≥1985)(P × PM × M))

	
Plan lógico:
				πpName,mStadium,goals
						|
	σP.pId=PM.pId ∧ PM.mId=M.mId ∧ P.pYear ≥1985
					   /|\
					  P PM M

```

### Casos mas complejos:
- Considerar otros operadores (unión, intersección, etc).
- Considerar consultas anidadas y correlacionadas.
- Considerar vistas.

---

## Set planes lógicos para ser optimizado

![[Pasted image 20240502190156.png]]
