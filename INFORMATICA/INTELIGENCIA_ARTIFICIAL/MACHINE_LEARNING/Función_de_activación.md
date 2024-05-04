### Definición


### Funciones de Activación: 
#### Sigmoid

**Función Sigmoidal:**
σ(x) = 1 / (1 + e^(-x))
∂σ(x) / ∂x = σ(x)(1 − σ(x))

**Buenas propiedades:**
- Suave y diferenciable.
- Empuja los valores de salida hacia los extremos, bueno para clasificación.

**Mala propiedad 1:**
- Las neuronas saturadas tienen gradientes cercanos a cero.
- La actualización de pesos utiliza descenso de gradiente.

