### Índice

- **Definición**
- **Operadores relacionales**
- **Ejemplo de consultas en álgebra relacional**
- **Semántica álgebra relacional**
- **Álgebra relacional extendida**

---

### Definición

- Lenguaje de consultas procedural.
- Basado en operadores relacionales (algebra).

---

### Operadores relacionales

#### Selección: `σ_{θ}` (R).
- θ es una combinación booleana (∧, ∨) de términos:
    - atributo1 op atributo2
    - atributo op constante
- con op ∈ {=, ≤, ≥, <, >}.

#### Proyección: πL(R).
- L = lista de atributos.

#### Operaciones de conjunto:
- Unión: R1 ⋃ R2
- Intersección: R1 ⋂ R2
- Diferencia: R1 − R2

#### Joins:
- Producto cruz: R1 × R2
- θ-join: R1 &θ R2 = σθ (R1 × R2)
    - θ es una combinación booleana (∧, ∨) de términos:
        - atributo1 op atributo2
        - atributo op constante
    - con op ∈ {=, ≤, ≥, <, >}.
- Equi-join: R1 &φ R2 = σφ(R1 × R2)
    - φ solo contiene igualdades.
- Natural-join: R1 & R2 = σφ(R1 × R2)
    - φ = ⋀a∈att(R1)∩att(R2) R1.a = R2.a.

---

### Ejemplo de consultas en álgebra relacional

```
- πname,year(P)
- σgoals≥100 ∨ goals≤60(P)
- πgoals ( σyear=1990(P) )
- P &P.id=M.id M
- P & M
```

---

### Semántica álgebra relacional

Dos tipos de semánticas para álgebra relacional:
- Set semantics.
- Bag semantics (SQL).

---

### Álgebra relacional extendida

- Rename: ρold att→new att(R)
- Eliminación de duplicados: δ(R)
- Group-by con agregación: γG ,A(R)
    - G : lista de atributos ha agrupar.
    - A: lista de elementos de la forma: f (agg att) → new att
        - con f ∈ {MIN, MAX, SUM, AVG, ...}.
- Sorting (ordenar): τ (R)
#### Ejemplos

- P & ρmgoals→goals (M)
- δ(πname,year(P))
- γyear,AVG(goals)→GperY (P)

