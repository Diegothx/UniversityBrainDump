---
name: ResNet. Deep Residual Learning for Image Recognition
Github: https://github.com/pytorch/vision/blob/main/torchvision/models/resnet.py
Paper: https://arxiv.org/pdf/1512.03385
topic:
  - machine learning
Date: 10 Dec 2015
Organization: Microsoft Research
Authors: Kaiming He, Xiangyu Zhang, Shaoqing Ren, Jian Sun
---
## Puntos Clave
- **Aprendizaje Residual**: Introduce el concepto de aprendizaje residual para facilitar el entrenamiento de redes muy profundas.
- **Arquitecturas ResNet**: Propone modelos ResNet con profundidades que van desde 50 hasta 152 capas, empleando conexiones de salto para permitir el flujo de información entre capas.
- **Bloques residuales**: Conjunto de capas en donde a la salida se suma la entrada original (residuo) con la salida de las convoluciones.
- **Resultados Experimentales**: Logra un rendimiento de vanguardia en el conjunto de datos ImageNet, superando arquitecturas anteriores con márgenes significativos.
- **Análisis**: Realiza un análisis exhaustivo para comprender los beneficios del aprendizaje residual y el impacto de la profundidad de la red en el rendimiento.
## Contexto
Agregar más capas a una red profunda generalmente mejora el rendimiento en problemas de clasificación de imágenes y otras tareas de reconocimiento visual.
## Problema de degradación
Sin embargo, después de un punto de saturación, surge el problema de degradación, donde agregar más capas resulta en un modelo que degrada rápidamente al saturar su precisión obteniendo un mayor error de entrenamiento. Este problema sugiere que las redes tienen dificultades para aproximar mapeos de identidad a través de múltiples capas no lineales (En teoría, mayor capas no debería llevar a peor rendimiento, puesto que una red de 10 capas es equivalente a una de 20 pero con 10 capas de convulsiones de identidad (donde AI=A). En la practica, esto no pasa, por lo menos no en un tiempo factible)
## Propuesta
Ante esta problemática, se propone un framework basado en aprendizaje RESIDUAL, el cual se fundamenta en la hipótesis de que es más fácil de optimizar. Los resultados muestran que es más sencillo de optimizar en conjuntos de datos como ImageNet y CIFAR-10, permitiendo agregar más capas sin aumentar la pérdida de rendimiento. Esto sugiere que es algo general y no una característica específica del sistema. También se introduce una arquitectura "bottleneck" para reducir la complejidad computacional.
## Trabajos previos:
- Representación residual: In image recognition, VLAD
[18] is a representation that encodes by the residual vectors
with respect to a dictionary, and Fisher Vector [30] can be
formulated as a probabilistic version [18] of VLAD. Both
of them are powerful shallow representations for image re-
trieval and classification [4, 48]. For vector quantization,
encoding residual vectors [17] is shown to be more effec-
tive than encoding original vectors.
In low-level vision and computer graphics, for solv-
ing Partial Differential Equations (PDEs), the widely used
Multigrid method [3] reformulates the system as subprob-
lems at multiple scales, where each subproblem is respon-
sible for the residual solution between a coarser and a finer
scale. An alternative to Multigrid is hierarchical basis pre-
conditioning [45, 46], which relies on variables that repre-
sent residual vectors between two scales. It has been shown
[3, 45, 46] that these solvers converge much faster than stan-
dard solvers that are unaware of the residual nature of the
solutions. These methods suggest that a good reformulation
or preconditioning can simplify the optimization
- Shortcut Connections: Usado para la operacion F+x junto a element-wise adition, el cual se aplicado en 2 mapas de features, channel by channel, Para paliarlos, se han propuesto nuevas arquitecturas en las que la información es capaz de avanzar hacia la salida a través de _**conexiones atajo**_, saltándose capas intermedias. Se trata de un procedimiento que tendría cierta analogía con las células piramidales del cerebro, que comunican áreas profundas del encéfalo con la corteza, sin necesidad de que éstos sean transportados por las neuronas de las capas intermedias.
## Aprendizaje Residual
La ecuación y = F(x, {W_{i}}) + x no produce más parámetros ni mayor complejidad computacional, permitiendo comparar con sistemas planos ya que cuentan con los mismos parámetros. Si x y F no son de las mismas dimensiones, se necesita hacer una proyección lineal por la shortcut connection para igualar las dimensiones: F(x,{w_{i}}) + W_{s}x. F es flexible y puede ser de 2, 3, 4 capas, etc. No se recomienda 1 capa ya que la ecuación termina acercándose a una función lineal, lo cual no entrega beneficios adicionales. Si el óptimo son mapeos de identidad, una red residual aproxima los pesos de las transformaciones a 0 para modelar la identidad, debido a que el residuo sería la única parte que importa. En la práctica, es raro que los mapeos de identidad sean los óptimos, pero ayuda a encontrar las perturbaciones con referencia a este mapeo, tratándolo como una función nueva.
## Bloques residuales:
Conjunto de capas en una red neuronal que conectan un "salto" de la entrada (residuo) a estas con la salida de haber pasado el input por las capas. Este salto finalmente significa que, siendo F(x, {Wi}) la operación que representa el pasar por todas las capas, la salida final de este bloque sea F(x, {Wi}) + x en vez de simplemente F(x, {Wi}). En el ejemplo de ResNet18, estos bloques son de la forma F=W_{2}σ(w_{1}x) + x, donde W1 y W2 son los pesos de las capas convoluciones y σ representa la funciones de activación ReLU.

 Conjunto de capas en una red neuronal que al final se suma el resultado de todo el bloque con la entrada original. Un ejemplo, un bloque F=W_{2}o(w_{1}x), , donde W1y W2​ son los pesos de las capas convoluciones y o representa la operación realizada por estas capas, que típicamente incluyen funciones de activación como ReLU para introducir no linealidades.
 
| Residual Block | Layers   | Architecture                                                           |
| -------------- | -------- | ---------------------------------------------------------------------- |
| Basic          | 2 layers | Conv3x3, BatchNorm, ReLU, Conv3x3, BatchNorm                           |
| Bottleneck     | 3 layers | Conv1x1, BatchNorm, ReLU, Conv3x3, BatchNorm, ReLU, Conv1x1, BatchNorm |

## Arquitecturas ResNet

| Layer | Output Size | 18-layer                             | 34-layer                             | 50-layer                                           | 101-layer                                          | 152-layer                                          |
| ----- | ----------- | ------------------------------------ | ------------------------------------ | -------------------------------------------------- | -------------------------------------------------- | -------------------------------------------------- |
| conv1 | 112×112     | 7×7, 64, stride 2                    | 7×7, 64, stride 2                    | 7×7, 64, stride 2                                  | 7×7, 64, stride 2                                  | 7×7, 64, stride 2                                  |
| conv2 | x 56×56     | 3×3 max pool, stride 2               | 3×3 max pool, stride 2               | 3×3 max pool, stride 2                             | 3×3 max pool, stride 2                             | 3×3 max pool, stride 2                             |
|       |             | [ 3×3, 64 <br>[ 3 x 3 64] x2         | [ 3×3, 64 <br>[ 3 x 3 64] x3         | [ 3×3, 64 <br>[ 3 x 3 64] x3                       | [ 3×3, 64 <br>[ 3 x 3 64] x3                       | [ 3×3, 64 <br>[ 3 x 3 64] x3                       |
| conv3 | x 28×28     | [ 3×3, 128 <br>[ 3 x 3 128] x2       | [ 3×3, 128 <br>[ 3 x 3 128] x4       | [ 1×1, 128<br>  3×3, 128   x4<br>  1×1, 512 ] <br> | [ 1×1, 128<br>  3×3, 128   x4<br>  1×1, 512 ] <br> | [ 1×1, 128<br>  3×3, 128   x8<br>  1×1, 512 ] <br> |
| conv4 | x 14×14     | [ 3×3, 256 <br>[ 3 x 3 256] x2       | [ 3×3, 256                           | [ 3×3, 256                                         | [ 3×3, 256                                         | [ 3×3, 256                                         |
| conv5 | x 7×7       | [ 3×3, 512 <br>[ 3x3 ,512] x2        | [ 3×3, 512                           | [ 3×3, 512                                         | [ 3×3, 512                                         | [ 3×3, 512                                         |
|       |             | 1×1 average pool, 1000-d fc, softmax | 1×1 average pool, 1000-d fc, softmax | 1×1 average pool, 1000-d fc, softmax               | 1×1 average pool, 1000-d fc, softmax               | 1×1 average pool, 1000-d fc, softmax               |
| FLOPs | -           | 1.8×10^9                             | 3.6×10^9                             | 3.8×10^9                                           | 7.6×10^9                                           | 11.3×10^9                                          |

| Model      | Layers (total) | Architecture                                                    |
| ---------- | -------------- | --------------------------------------------------------------- |
| ResNet-18  | 18             | Conv1, MaxPool, Residual blocks (2 layers each: [2, 2, 2, 2])   |
| ResNet-34  | 34             | Conv1, MaxPool, Residual blocks (2 layers each: [3, 4, 6, 3])   |
| ResNet-50  | 50             | Conv1, MaxPool, Residual blocks (3 layers each: [3, 4, 6, 3])   |
| ResNet-101 | 101            | Conv1, MaxPool, Residual blocks (3 layers each: [3, 4, 23, 3])  |
| ResNet-152 | 152            | Conv1, MaxPool, Residual blocks (3 layers each: [3, 8, 36, 3])  |
| ResNet-200 | 200            | Conv1, MaxPool, Residual blocks (3 layers each: [3, 24, 36, 3]) |
## Codigo
### Conv3x3:
``` python
def conv3x3(in_channels, out_channels, stride=1):
    return nn.Conv2d(in_channels, out_channels, kernel_size=3,
                     stride=stride, padding=1, bias=False)
```
### Bloque Residual:

``` python
class ResidualBlock(nn.Module):
    def __init__(self, in_channels, out_channels, stride=1, downsample=None):
        super(ResidualBlock, self).__init__()
        self.conv1 = conv3x3(in_channels, out_channels,
                     stride=stride)
        self.bn1 = nn.BatchNorm2d(out_channels)
        self.relu = nn.ReLU(inplace=True)
        self.conv2 = conv3x3(out_channels, out_channels)
        self.bn2 = nn.BatchNorm2d(out_channels)
        self.downsample = downsample

    def forward(self, x):
        residual = x
        if self.downsample:
            residual = self.downsample(residual)
        out = self.conv1(x)
        out = self.bn1(out)
        out = self.relu(out)
        out = self.conv2(out)
        out = self.bn2(out)
        out += residual
        out = self.relu(out)
        return out  
```
### BottleNeck
``` python
class Bottleneck(nn.Module):

    expansion: int = 4

    def __init__(
        self,
        inplanes: int,
        planes: int,
        stride: int = 1,
        downsample: Optional[nn.Module] = None,
        groups: int = 1,
        base_width: int = 64,
        dilation: int = 1,
        norm_layer: Optional[Callable[..., nn.Module]] = None,
    ) -> None:
        super().__init__()
        if norm_layer is None:
            norm_layer = nn.BatchNorm2d
        width = int(planes * (base_width / 64.0)) * groups
        # Both self.conv2 and self.downsample layers downsample the input when stride != 1
        self.conv1 = conv1x1(inplanes, width)
        self.bn1 = norm_layer(width)
        self.conv2 = conv3x3(width, width, stride, groups, dilation)
        self.bn2 = norm_layer(width)
        self.conv3 = conv1x1(width, planes * self.expansion)
        self.bn3 = norm_layer(planes * self.expansion)
        self.relu = nn.ReLU(inplace=True)
        self.downsample = downsample
        self.stride = stride

    def forward(self, x: Tensor) -> Tensor:
        identity = x

        out = self.conv1(x)
        out = self.bn1(out)
        out = self.relu(out)

        out = self.conv2(out)
        out = self.bn2(out)
        out = self.relu(out)

        out = self.conv3(out)
        out = self.bn3(out)

        if self.downsample is not None:
            identity = self.downsample(x)

        out += identity
        out = self.relu(out)

        return out
```
### ResNet:
``` python
class ResNet(nn.Module):
    def __init__(self, in_channels, block, layers, num_classes=10):
        super(ResNet, self).__init__()
        self.in_channels = in_channels
        self.conv1 = nn.Conv2d(3, self.in_channels, kernel_size=7,stride=2)
        self.maxpool = nn.MaxPool2d(kernel_size=3, stride=2)
        self.bn1 = nn.BatchNorm2d(self.in_channels)
        self.relu = nn.ReLU(inplace=True)
        self.layer1 = self.make_layer(block, self.in_channels, layers[0])
        self.layer2 = self.make_layer(block, self.in_channels * 2, layers[1], stride=2)
        self.layer3 = self.make_layer(block, self.in_channels * 4, layers[2], stride=2)
        self.layer4 = self.make_layer(block, self.in_channels * 8, layers[3], stride=2)
        self.avgpool1 = nn.AdaptiveAvgPool2d((1, 1))
        self.fc = nn.Linear(self.in_channels * 8, num_classes)

    def make_layer(self, block, out_channels, blocks, stride=1):
        downsample = None
        if (stride != 1) or (self.in_channels != out_channels):
            downsample = nn.Sequential(
                conv3x3(self.in_channels, out_channels, stride=stride),
                nn.BatchNorm2d(out_channels))
        layers = []
        layers.append(block(self.in_channels, out_channels, stride, downsample))
        self.in_channels = out_channel
        for i in range(1, blocks):
            layers.append(block(out_channels, out_channels))
        return nn.Sequential(*layers)

    def forward(self, x):
        out = self.conv1(x)
        out = self.bn1(out)
        out = self.relu(out)
        out = self.maxpool(out)
        out = self.layer1(out)
        out = self.layer2(out)
        out = self.layer3(out)
        out = self.layer4(out)
        out = self.avgpool1(out)
        out = torch.flatten(out, 1)
        out = self.fc(out)
        return nn.functional.softmax(out, dim=1)
```
### Modelos:
``` python
ResNet18 = ResNet(BasicBlock, [2, 2, 2, 2], num_classes)

ResNet34 = ResNet(BasicBlock, [3, 4, 6, 3], num_classes)

ResNet50 = ResNet(Bottleneck, [3, 4, 6, 3], num_classes)

ResNet101 = ResNet(Bottleneck, [3, 4, 23, 3], num_classes)

ResNet152 = ResNet(Bottleneck, [3, 8, 36, 3], num_classes)
```
## Resultados Experimentales: 
Se presentan arquitecturas con más de 100 o incluso 1000 capas, donde se destaca la eficacia de las capas de 157, siendo menos complejas que las VGG nets. Además, se menciona que ResNet ganó el primer lugar en varias competiciones de clasificación y detección de imágenes.

capas de mas de 100 o 1000 capas. Capas de 157 presenta buenos resultados siendo menos complejo que VGG nets. Gano primer lugar en la competencia de clasificacion ILSVRC 2015 y ImageNet detection, ImageNet localization, COCO detection, and COCO segmentation in ILSVRC & COCO 2015 competitions.
Bottleneck Architectures
2 layer: 18,34,52,
3 layer: 50,101,152 , menor complejidad que VGG-16/19, medido por diferencias en FLOPs
Resultados:
:
resNet1202, peor que el 110, aunque ambos presentan similar error de entrenamiento, sugiriendo overfitting.
Comparacion plano, vgg y residual:
* Argumenta que la mejora no se debe da por vanishing gradientes, pues los modelos planos son entrenados con BN, impidiendo que forward or backward signals vanish. 
* Identity vs Projection shortcut A,B,C 
## Análisis
