##
![[Pasted image 20240425205550.png]]
*Las DBSM Funcionan en la memoria secundaria (normalmente [[disco_duro]]) dentro de la [[Jerarquía_de_memoria]]
## Índice

- **Almacenamiento basado en archivos**
- **Almacenamiento basado en páginas**
- **Alternativas**

---

##  Almacenamiento basado en archivos

Posibilidades de diseño:
1. Un archivo del SO para todas las relaciones (ej. SQLite).
2. Un archivo del SO por cada relación.
3. Varios archivos del SO por cada relación.

#### ¿Alguna desventaja?


---

## Almacenamiento basado en páginas
- Una página corresponde a un bloque en disco.
- Cada relación corresponde a un set de páginas que contienen un subconjunto de las tuplas.
### Ventajas:
- Manejo granular del contenido en disco.
- Optimización del acceso al disco.
- Facilidad para el manejo de transacciones.
### Desventajas:
### [[HeapFiles]]: Estructura de datos que enfrenta varias aristas del problema.

---

## Alternativas

### DBMS orientados a columnas

**Ventajas de los DBMS orientados a columnas:**

- La información puede ser compactada.
- Mayor eficiencia en operaciones basadas en columnas.

**Ventajas de los DBMS orientados a filas:**

- Mayor eficiencia en escritura.
- Mayor eficiencia en acceso por tuplas completas.

Propuesto en el artículo:
[*C-Store: A column oriented DBMS*](https://web.stanford.edu/class/cs345d-01/rl/cstore.pdf) por Stonebreaker et al.

Existen sistemas comerciales y académicos en el mercado:

- **Vertica (C-store):** Inicialmente desarrollado en MIT.
- **MonetDB:** Desarrollado por CWI Amsterdam.
