Dispositivo I/O que es la principal fuente de almacenamiento secundario (no volátil).
### Componentes:
  ![[Pasted image 20240425202954.png]]
  ![[Pasted image 20240425203023.png]]
  ![[Pasted image 20240425203042.png]]
  
- Funcionamiento
	
- Ejemplo de disco duro (MiDiscoDuro 747)
- Zone bit recording
- Sectores vs Bloques vs Páginas

### Demora en la búsqueda de los datos
- Tiempos de busqueda en un disco duro pueden ser significativos, por esto se deja Datos relacionados almacenados en bloques contiguos e intenta Minimizar n´umero de accesos a disco duro.
- Tiempo total = Tiempo de búsqueda (tiempo en posicionar cabeza en el track, 3 ms → 15 ms (promedio)) + Retraso rotacional (tiempo en rotar el disco hasta el primer sector, 2 ms → 7 ms (promedio)) + Velocidad de transferencia (tiempo en leer los sectores (incl. gaps), 0ms (dependiendo del tama˜no de los datos))
- Ejemplo de tiempos de demora:
	MiDiscoDuro 747
	Rotaci´on: 7200 rpm (8.33 ms por vuelta).
	Movimiento brazo:
	1 ms para empezar/parar brazo.
	1 ms por cada 4000 tracks.
	Numero de tracks: 65.536 tracks.
	Transferencia de datos: 0.13 ms por bloque.
	Min Max Promedio
	Seek 0 ms 17.38 ms 6.46 ms
	Rotational 0 ms 8.33 ms 4.17 ms
	Transfer 0.13 ms 0.13 ms 0.13 ms
	Total 0.13 ms 25.84 ms 10.76 ms
	¿cu´anto es la demora si debo buscar 1000 bloques distintos?
### Escritura de bloques
- Costo similar al de leer un bloque o peor
- Proceso de escritura: Leer bloque, Modificar el bloque en memoria y Escribir el bloque.


### Optimizaciones para manejo del almacenamiento secundario
"El tiempo para acceder al disco es mucho mayor que el tiempo
necesario para manejar datos en memoria. Entonces, el n´umero de
accesos a disco (Disk I/O) es una buena aproximaci´on al tiempo
del algoritmo y, por lo tanto, debe ser minimizado.”
Garcia-Molina, Ullman, & Widom, 2008
1. Localidad de los datos:
   Si las tuplas t y t′ son parte de la misma relaci´on R, entonces almacenar t:
	en el mismo bloque que t′, o, si no,
	en el mismo track que t′, o, si no,
	en el mismo cilindro que t′, o, si no,
	en el cilindro contiguo a t′.
2. Pre-fetch:
	Adivinar y traer bloques ser´an solicitados en el futuro.
	Para una solicitud de un bloque k, traer tambi´en bloques contiguos
	k + 1, k + 2, . . . , k + N
	(N es una cantidad fijada por el usuario).
3. Planificaci´on del disco.
	Dada una secuencia de solicitudes o₁, . . . , oₙ al disco, reordenar el orden de las solicitudes a oᵢ₁, . . . , oᵢₙ de tal forma de minimizar el tiempo total.
	
	**Ejemplo (Algoritmo del ascensor):**
	El disco es como un ascensor, donde sus pisos son los cilindros del disco. El brazo del disco mantiene su dirección si quedan solicitudes por delante.
	
| Cilindro solicitado | Llegada solicitud | Tiempo Elevador | Tiempo FIFO |
| ------------------- | ----------------- | --------------- | ----------- |
| 8000                | 0 ms              | 8 ms            | 8 ms        |
| 24000               | 0 ms              | 24 ms           | 24 ms       |
| 36000               | 0 ms              | 36 ms           | 36 ms       |
| 5000                | 10 ms             | 87 ms           | 67 ms       |
| 46000               | 20 ms             | 46 ms           | 108 ms      |
| 30000               | 30 ms             | 62 ms           | 124 ms      |
	
	*Considera una velocidad del brazo de 1000 tracks por ms.*
4. Multiples discos:
	Distribuir tuplas en multiples discos para asi tener acceso concurrente a las
	relaciones y tuplas
5. Multiples copias (mirroring)
	Mantener multiples copias de los datos en distintos discos para:
	Disponibilidad de los datos en caso de fallas.
	Mayor velocidad al leer datos (pero menor velocidad al escribir).
#### Casos imporantes:
	A. Procesamiento Batch (OLAP = On-Line Analytical Processing)
	• Consultas que no requieren una respuesta instant´anea.
	• Un solo proceso usando el disco.
	• Accesos al disco pueden ser predecidos.
	B. Procesamiento Online (OLTP = On-Line Transaction Processing)
	• Consultas requieren una respuesta instant´anea.
	• Varios procesos accediendo el disco.
	• Poca capacidad de predicci´on de accesos al disco.
#### Comparación de optimizaciones
|                                     | A           | B                                           |
| ----------------------------------- | ----------- | ------------------------------------------- |
| Localidad de los datos              | Bueno       | No tan útil                                 |
| Pre-fetch                           | Bueno       | Poca ayuda                                  |
| Planificación del disco             | No tan útil | Bueno, especialmente con muchas solicitudes |
| Múltiples discos y múltiples copias | Bueno       | Bueno, especialmente para B                 |

### Nuevas tecnologías
- Discos de estado sólido (SSD)
	Misma interfaz I/O.
	No hay demora “mecánica” ni espacial.
	Asimetría entre escritura y lectura.
	“Borrar antes de escribir”.
	Mejores propiedades físicas y energéticas
| Sigla         | Nombre del Curso                                               |      |
| ------------- | -------------------------------------------------------------- | ---- |
| 1-2020        |                                                                |      |
| MAT1203       | Álgebra Lineal                                                 |      |
| MAT1610       | Cálculo I                                                      |      |
| QIM100E       | Química para Ingeniería                                        |      |
| 2-2020        |                                                                |      |
| FIS0154       | Laboratorio de Dinámica                                        |      |
| FIS1514       | Dinámica                                                       |      |
| ICS1513       | Introducción a la Economía                                     |      |
| IIC1103       | Introducción a la Programación                                 |      |
| MAT1620       | Cálculo II                                                     |      |
| 1-2021        |                                                                |      |
| BIO141C       | Biología de la Célula                                          |      |
| ICM2313       | Diseño Gráfico                                                 |      |
| IIC2233       | Programación Avanzada                                          |      |
| IRB2001       | Fundamentos de Robótica                                        |      |
| MAT1630       | Cálculo III                                                    |      |
| 2-2021        |                                                                |      |
| EYP1113       | Probabilidades y Estadística                                   |      |
| FIS0152       | Laboratorio de Termodinámica                                   |      |
| FIS0153       | Laboratorio de Electricidad y Magnetismo                       |      |
| FIS1523       | Termodinámica                                                  |      |
| FIS1533       | Electricidad y Magnetismo                                      |      |
| IIC1253       | Matemáticas Discretas                                          |      |
| IIC2413       | Bases de Datos                                                 |      |
| MAT1640       | Ecuaciones Diferenciales                                       |      |
| 1-2022        |                                                                |      |
| ARO102M       | Animación desde las Artes Visuales                             |      |
| DNO016        | Diseno y Manufactura Digital Cad/Cam                           |      |
| ICS1113       | Optimización                                                   |      |
| IIC2513       | Tecnologías y Aplicaciones Web                                 |      |
| 2-2022        |                                                                |      |
| ICM2503       | Procesos de Manufactura                                        |      |
| IEE2713       | Sistemas Digitales                                             |      |
| IIC2143       | Ingeniería de Software                                         |      |
| IIC2343       | Arquitectura de Computadores                                   |      |
| 1-2023        |                                                                |      |
| ICM2803       | Dinámica de Sistemas Mecánicos                                 |      |
| IEE2103       | Señales y Sistemas                                             |      |
| IIC2213       | Lógica para Ciencias de la Computación                         |      |
| IIC2333       | Sistemas Operativos y Redes                                    |      |
| IIC2613       | Inteligencia Artificial                                        |      |
| MEB174        | Fgm Solidaridad y Primeros Auxilios                            |      |
| 2-2023        |                                                                |      |
| EDU0160       | Desarrollo y Aprendizaje del Adolescente                       |      |
| EDU0161       | Gestión de Aulas Heterogéneas                                  |      |
| IEE2123       | Circuitos Eléctricos                                           |      |
| IEE2613       | Control Automático                                             |      |
| IIC2133       | Estructuras de Datos y Algoritmos                              |      |
| ING1001       | Práctica I                                                     |      |
| IRB2002       | Diseño de Sistemas Robóticos (Capstone)                        |      |
| 1-2024        |                                                                |      |
| IIC3253       | Criptografía y Seguridad Computacional                         |      |
| IIC3697       | Aprendizaje Profundo                                           |      |
| IIC3413       | Implementación de Sistemas de Base de Datos                    | 5/22 |
| DPT5000       | Acondicionamiento Fisico con Pesas I                           |      |
| Extra         |                                                                |      |
| Planner       | Desarrollo de WebApp en React                                  |      |
| Caleuche      | Desarrollo de proyecto en robotica Software                    |      |
| Ipre Rocas    | Uso de IA para la deteccion de rocas por img (EN DEEPLEARNING) |      |
| Ipre Sensores | Uso de Lidar y sensores de robotica                            |      |
.
	Mucho mas costoso.
- Almacenamiento en la red
- Almacenamiento en la nube
- Ventajas y desventajas