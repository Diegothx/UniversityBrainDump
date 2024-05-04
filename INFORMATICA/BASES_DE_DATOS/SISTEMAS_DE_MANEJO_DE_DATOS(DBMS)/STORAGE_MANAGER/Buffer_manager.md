### ![[Pasted image 20240425234848.png]] 
## *Índice*

- **Definición**
- **Almacenamiento basado en páginas**
- **Alternativas**

---

## Definición 
El **Buffer manager** actúa como mediador entre el disco y la memoria principal. Cuenta con una cantidad restringida de memoria RAM. Las páginas son traídas a memoria a pedido y se encarga de decidir qué páginas eliminar cuando el buffer está lleno.
![[Pasted image 20240425234635.png]]

---

## Características de cada frame:

Cada frame tiene dos variables:
1. `#pin`: cantidad de procesos que están usando esta página.
2. `Dirty`: indica si el contenido es necesario guardarlo en memoria.

Los procesos acceden al **Buffer manager** usando las funciones `pin(pageno)` y `unpin(pageno, dirty)`.


---

## Interfaz para acceder al buffer manager:

### Función `pin(pageno)`

1. Solicita la página `pageno` al buffer manager.
2. Si la página `pageno` no está en memoria:
   - Se selecciona un frame vacío `X`.
   - Se trae la página `pageno` a memoria y se carga en `X`.
   - Se actualiza `X.#pin := 1` y `X.dirty := false`.
3. Si la página `pageno` está en memoria, se incrementa su `#pin` en 1.
4. El Buffer manager retorna una referencia al frame que contiene `pageno`.

#### Problemas:

- ¿Qué ocurre si no hay un frame vacío?
- ¿Qué ocurre si todos los frames tienen `#pin` mayor que 1?

### Función `unpin(pageno, dirty)`

1. Solicita la liberación de la página `pageno` (almacenada en el frame `X`).
2. Se decrementa el `X.#pin` en uno.
3. Se actualiza `X.dirty := true` si la página fue modificada.

#### Problemas:

- ¿Cuándo son guardados los datos de la página en disco?
- ¿Qué ocurre si un proceso nunca hace unpin de su página?


---

## Requisitos de la interfaz pin y unpin:

1. Cada proceso (de una BD) debe mantener las funciones `pin` y `unpin` correctamente anidadas.
d ← pin(p); ⋮ (proceso lee y usa los datos en la dirección de memoria d) ⋮ unpin(p, false);
2. Cada proceso (de una BD) debe liberar una página lo antes posible.


---

## Escritura concurrente de una página:

Suponga lo siguiente:
- La misma página `p` es solicitada por más de un proceso (`#pin > 0`).
- Más de un proceso modifica el mismo dato de `p`.

¿Qué hacemos?

... estos conflictos son manejados por el administrador de transacciones (lo veremos más adelante).


---

## Políticas de reemplazo (replacement policies):

La efectividad del buffer manager depende directamente de la política de reemplazo usada. Hay diferentes tipos de políticas o heurísticas como FIFO, Least Recently Used (LRU), Clock, Random. Es importante recordar que estas son heurísticas y pueden fallar.


---

## Buffer manager en la práctica:

Varias técnicas adicionales incluyen Prefetching, Page fixing / hating, y Buffer particionado.


---

## Base de Datos vs. Sistemas Operativos:

¿Cuáles son las ventajas de usar un buffer manager propio?

- Mayor conocimiento del patrón de acceso a los datos.
- Política de reemplazo propia.
- Hacer pinned y unpinned de las páginas.
- Forzar el almacenamiento de páginas.
- Acceso "fino" por parte del administrador de transacciones.
