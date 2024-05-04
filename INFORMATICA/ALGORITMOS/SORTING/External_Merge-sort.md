- Versión de [[Merge-sort]] usada cuando el tamaño de los datos a ordenar excede por mucho el espacio disponible en RAM, típicamente utilizado en bases de datos.

---

## Definición
- **Run**: Secuencia de páginas que contiene un conjunto ordenado de tuplas.
- **External merge sort** funciona por fases:
  - **Fase 0**: Creación de los runs iniciales.
  - **Fase i**:
    1. Traemos los runs desde el disco a la memoria de a pares.
    2. Hacemos merge de cada par de runs.
    3. Almacenamos el nuevo run de regreso al disco
	![[Pasted image 20240503155023.png]]

---

## Ejemplo
	![[Pasted image 20240503155042.png]]

---

## Algoritmo
**Input:** Páginas P = {p1, . . . , pn} con tuplas.
**Output:** Lista de páginas P' con las tuplas ordenadas.
```python
externalMergeSort(P):
    foreach p ∈ P do # Fase 0
        Leer p a memoria
        Ordenar p en r
        Escribir r en disco
    R := {r1, . . . , rn} # Runs iniciales
    while |R| > 1 do # Fase i
        R' := ∅
        foreach r1, r2 ∈ R do # Para cada par de runs
            Leer r1 y r2 a memoria (R := R - {r1, r2})
            Hacer merge de r1 y r2 (r := Merge(r1, r2))
            Escribir r en disco (R' := R' ∪ {r})
        R := R'
    return único run en R
````

---

## Eficiencia (en I/O) de External Merge Sort

Considerando N como el número de páginas del archivo:

- Cada fase lee y escribe cada página del archivo.
- Numero de fases:
    - 1 + ⌈log2 N⌉
- Costo total:
    - 2 * N * (1 + ⌈log2 N⌉)
### Ejemplo
Considerando un archivo de 8GB con páginas de 8KB (aprox. 1,048,576 páginas): 
Total de I/O = 44,040,192 I/O. 
Si cada I/O toma ≈ 0.1ms (mínimo): 1.2 horas (tiempo total).

---

## Optimizaciones
- Aumentar el número de runs en el merge. Contamos con B + 1 paginas en buffer en vez de 3
 ![[Pasted image 20240503160304.png]]
- Reducir el número de runs iniciales. Usamos las B + 1 paginas en buffer para crear un runs iniciales con B + 1 paginas.
	 ![[Pasted image 20240503160349.png]]
- Evitar escribir el último resultado (pipeline). Si consideramos que:
	1. Sorting es una operación mas en nuestro plan físico.
	2. Tuplas ordenadas son recibidas por otro operador (pipeline).
	Podemos evitar escribir el ultimo resultado en disco!!
	En este caso: costo de External Merge Sort ≈ 3 ⋅ N
- Algoritmo optimizado:
```python
externalMergeSort(P):
    while P ≠ ∅ do # Fase 0
        Leer {p1, . . . , pB+1} a memoria (P := P - {p1, . . . , pB+1})
        Ordenar {p1, . . . , pB+1} (quicksort) y crear un run r
        Escribir r en disco
    R := {r1, . . . , r n_B+1}
    while |R| > 1 do # Fase i
        R' := ∅
        while R ≠ ∅ do # Para B runs
            Leer r1, . . . , rB a memoria (R := R - {r1, . . . , rB})
            Hacer merge de r1, . . . , rB (r := Merge(r1, . . . , rB))
            Escribir r en disco (R' := R' ∪ {r})
        R := R'
    return único run en R
```

---

## Eficiencia (en I/O) de External Merge Sort Optimizado
Considerando N como el número de páginas del archivo y B + 1 el tamaño del buffer:
- ¿Cuál es el número de runs iniciales?
    - ⌈N / (B + 1)⌉
- ¿Cuál es el máximo número de "fases"?
    - 1 + ⌈logB ⌈ N / (B + 1)⌉⌉
- ¿Cuál es el costo total en I/O?
    - 2 * N * (1 + ⌈logB ⌈ N / (B + 1)⌉⌉)
### Ejemplo

Considerando un archivo de 8GB con páginas de 8KB (aprox. 1,048,576 páginas) y un buffer de 2GB (B + 1 ≈ 262,145 páginas): Total de I/O = 6.7 minutos.

---

## Consideraciones sobre el tamaño del buffer
- Para efectos prácticos (N = número de páginas del input):
  1. Escogemos el tamaño del buffer (B) dinámicamente e igual a √N.
  2. Podemos asumir que External Merge Sort solo tarda 2 fases. Esto viene de que al despejar la ecuación 2 = 1 + ⌈logB ⌈ N / (B + 1)⌉⌉ da que B ≥ √N
     Costo de External Merge Sort ≈ 4 * N
     Con pipeline, costo de External Merge Sort ≈ 3  * N
![[Pasted image 20240503160731.png]]
```css
**Número de fases según el tamaño del buffer**

| N     | B=3 | B=5 | B=9 | B=17 | B=129 | B=257 | B=1000 |
|-------|-----|-----|-----|------|-------|-------|--------|
| 100   | 7   | 4   | 3   | 2    | 1     | 1     | 1      |
| 103   | 10  | 5   | 4   | 3    | 2     | 2     | 1      |
| 104   | 13  | 7   | 5   | 4    | 2     | 2     | 2      |
| 105   | 17  | 9   | 6   | 5    | 3     | 3     | 2      |
| 106   | 20  | 10  | 7   | 5    | 3     | 3     | 3      |
| 107   | 23  | 12  | 8   | 6    | 4     | 3     | 3      |
| 108   | 26  | 14  | 9   | 7    | 4     | 4     | 3      |
| 109   | 30  | 15  | 10  | 8    | 5     | 4     | 4      |
```
