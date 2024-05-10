- **Bucket de Datos Estático:** Un contenedor de datos que no se modifica, independientemente de la cantidad de datos almacenados en él (llamados entradas de datos o data-entries).
- **Operaciones en Hashing Estático:**
    - Para las operaciones de búsqueda (`search`), inserción (`insert`), y eliminación (`delete`):
        1. Computa el valor de hash para la clave `k`.
        2. Accede al bucket en la posición determinada por el valor de hash de `k`.
        3. Realiza la operación de búsqueda, inserción o eliminación en la página correspondiente a la posición calculada, o en sus páginas de desbordamiento si es necesario.
- **Costo de cada Operación:** Es 1 más el número de páginas de desbordamiento necesarias.
- **Colisión:** Ocurre cuando dos claves distintas (`k1` y `k2`) tienen el mismo valor de hash (`h(k1) = h(k2)`).
- **Selección de Función de Hash:** La meta es encontrar una función de hash `h` que minimice el número de colisiones para cualquier secuencia de valores `k1, k2, ..., kn`. Se busca que la función de hash se comporte de forma aleatoria sobre estos valores.
- **Rendimiento Óptimo:** Con una buena selección de función de hash y suponiendo que el número de entradas de datos (`data-entries`) es proporcional al tamaño de una página (`B`) multiplicado por el número de buckets (`N`), el costo de cada operación es aproximadamente constante: `#(data-entries) / (B * N)`.
- **Desafíos ante el Crecimiento de Datos:** Se presentan problemas si el número de entradas de datos (`data-entries`) crece significativamente. Por otro lado, si asignamos N muy grande sin tener muchos numeros de entrada de datos estamos desperdiciando memoria.