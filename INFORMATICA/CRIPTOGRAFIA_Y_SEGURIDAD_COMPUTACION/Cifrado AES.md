Los cálculos matemáticos del algoritmo de cifrado AES están basados en matrices de 4x4, con esto
logra los altos grados de eficiencia que lo han caracterizado.
A continuación, tenemos el código de una función en Python que convierte una cadena de caracteres en dicha matriz
``` python 
test_matrix =[
    [69, 120, 99, 101],
    [108, 101, 110, 116],
    [101, 123, 112, 117],
    [99, 125, 43, 38]
]
  def bytes2matrix(text):
	""" Convierte una cadena de 16 caracteres en una matriz de 4x4 """
	return [list(text[i:i+4]) for i in range(0, len(text), 4)]

def matrix2bytes(matrix):
	""" Convierte una matriz de 4x4 en una cadena de 16 caracteres"""
	return "".join(chr(matrix[i][j]) for i in range(4) for j in range(4))
```
`Excelente{puc}+&` 
