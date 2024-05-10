![[Pasted image 20240502171718.png]]
## Idea (Intuitiva):
Aumentar el número de buckets `N` dependiendo del número de `data-entries`.
## Estructura Básica:
1. Utilizar un directorio de buckets principales.
2. Duplicar el tamaño del directorio cuando haya un desbordamiento de un bucket.
## Profundidad Global y Profundidad Local:
### Profundidad Global (`n`):
- La función `hn` considera los `n` últimos bits.
- El directorio tiene un tamaño de `2^n`.
### Profundidad Local (`d`) para Cada Bucket:
- `hd(k) = hd(k')` para todo `k`, `k'` en el mismo bucket.
- Todos los elementos del bucket coinciden en los últimos `d` bits.
## Inserción en Extendable Hashing:
- Para insertar una llave `k` con profundidad global `n`:
    1. Calculamos `hn(k)` (los últimos `n` bits de `k`).
    2. Buscamos en el directorio el puntero `p` para la entrada `hn(k)`.
    3. Seguimos el puntero `p` al bucket `B` correspondiente.
    4. Si hay espacio en `B`, insertamos `k` en `B`.
    5. Si NO hay espacio en `B` con profundidad local `d`:
        - Hacemos un split del bucket `B`.
        - Aumentamos la profundidad local de `B0` y `B1` a `d + 1`.
        - Si `n > d`, insertamos `B0` y `B1` en el directorio.
        - Si `n = d`, duplicamos el tamaño del directorio.
### Split de Bucket:
- Dado un bucket `B` con profundidad local `d`, dividimos sus elementos en dos buckets según el bit `d + 1`.
### Duplicación del Directorio:
- Dado un bucket `B` con profundidad local `d = n`, hacemos un split de `B` y duplicamos el tamaño del directorio a `2n + 1`.
### Ejemplo:
- Consideremos el siguiente sistema de hashing con un directorio inicial de tamaño 4 (`2^2`).
```
	  2
	 ___
     0 0 -> [1100, 0000, 1010] | 1
h -> 0 1 \
     1 0 /  [0001, 0101, ] | 2
     1 1 -> [1111, 0011, ] | 2
```
- Insertamos la clave `1011` 
```
  	  2
	 ___
	 0 0 -> [1100, 0000, 1010] | 1
h -> 0 1 \
     1 0 /  [0001, 0101, ] | 2
     1 1 -> [1111, 0011, 1011] | 2

```
- Insertamos la claves `0010`
    - Al intentar insertar 0010, vemos que el bucket correspondiente está lleno. Por lo tanto, debemos hacer un split del bucket:
		- El bucket original contiene las claves `1100`, `0000`, `1010`.
		- Después del split, las claves se redistribuyen entre dos nuevos buckets (`B0` y `B1`) dependiendo del bit en la posición `d + 1`.
		- B0 = [1100,0000] B1=[1010]
		```
		  	  2
			 ___
			 0 0 -> [1100, 0000, ] | 2
		h -> 0 1 \  [1010, `0010`] | 2
		     1 0 /  [0001, 0101, ] | 2
		     1 1 -> [1111, 0011, 1011] | 2
		```
- Insertamos la claves `0111`
    - Al intentar insertar 0111, vemos que el bucket correspondiente está lleno. Por lo tanto, debemos hacer un split del bucket.
    - B0 = [1111, 0111, ] B1 = [0011, 1011]
    - Como `d > n` (3 > 2) duplicamos el directorio
		- Duplicamos el tamaño del directorio a `2n + 1` (2 \* 2+1 = 5), reorganizando los punteros de acuerdo con la nueva profundidad global.
		```
		  	  3
			 ___
			 000 -> [1100, 0000, ] | 2
		     100 /
		     001 -> [0001, 0101, ] | 2
		h -> 101 /
			 010 -> [1010, 0010] | 2
		     110 /
		     011 -> [0011, 1011] | 3 
		     111 -> [1111, 0111, ] | 3
		```
## Uso de Páginas de Desbordamiento:

- Aunque en principio, el hashing extensible no hace uso de páginas de desbordamiento, en algunos casos es prudente utilizarlas. ¿En qué circunstancias sería prudente usarlas?

## Eliminación en Extendable Hashing:

- Para eliminar, se busca el elemento `k*` y se elimina del bucket.
- Si el bucket queda vacío, se fusionan los buckets.
    - ¿Cuándo ocurre una disminución de la profundidad local?
    - ¿Cuándo ocurre una disminución de la profundidad global?
    - En general, la fusión de buckets se usa poco. ¿Por qué?

## Eficiencia de Extendable Hashing:
### Consideraciones:
- `D`: Tamaño (en páginas) del directorio.
- El costo de cada operación (en E/S):
    - Búsqueda: O(1)
    - Inserción: O(D)

---