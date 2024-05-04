*Modelo basado en [[ALGEBRA_RELACIONAL]]
### Definición

Una relación R está compuesta por:

1. **Esquema**: `name(att1 ∶ D1, . . . , attn ∶ Dn)`
    
    - `name`: nombre de la relación R.
    - `atti`: nombre del atributo i.
    - `Di`: dominio del atributo i.
    - `n`: aridad o cantidad de atributos de R.
2. **Instancia**: subconjunto finito de `D1 × . . . × Dn`.
    

Dada una relación R, usualmente denotaremos:

- `R`: como el nombre e instancia de R (si no hay confusión).
- `schema(R)`: como el esquema de R.
- `arity(R)`: como la aridad de R.
- `dom(atti)`: como el dominio Di del atributo atti.

### Propiedades de una relación

- El dominio de un atributo puede ser cualquier tipo primitivo: Números, Strings, Fechas, etc.
- Una relación R se ve como una tabla con filas y columnas.
    - Donde las filas son tuplas de R.
    - El orden de las filas no es importante.
    - Todas las filas son distintas.

---

### Base de Datos (BD) Relacional

#### Definición

1. Una BD relacional D es un conjunto finito de relaciones R1, . . . , Rm cada una con un nombre distinto.
    
    - `D = {R1, R2, . . . , Rm}`
2. Un esquema relacional S de una BD D es el conjunto de esquemas de las relaciones R1, . . . , Rm.
    
    - `schema(D) = {schema(R1), schema(R2), . . . , schema(Rm)}`

#### Ejemplo

- Esquema:
    ```sql
	Players(pName: CHAR, pTeam: CHAR, pBirth: DATE, pPosition: INT)
	Teams(tName: CHAR, tFans: INT, tBirth: DATE)
	```

- Instancia:
```php
Players: 
| pName          | pTeam          | pBirth       | pPosition | 
|----------------|----------------|--------------|-----------| 
| Alexis Sanchez | Inter de Milan | Dec 19, 1988 | 7         | 
| Gary Medel     | Bologna        | Ago 3, 1987  | 5         | 

Teams: 
| tName           | tFans | tBirth | 
|-----------------|-------|--------| 
| Bologna         | 38M   | 1909   | 
| Inter de Milan  | 80M   | 1908   |
```

#### Restricciones de Integridad

- Una restricción de integridad es una condición que restringe los datos que pueden ser almacenados en una BD relacional.
- Una BD D es válida con respecto a ϕ si satisface la restricción ϕ.
    - `D ⊧ ϕ`
- Ejemplos
	- Restricciones de dominio.
	- laves (Keys), Primary Key y Foreign Key.
	- Condiciones generales.
