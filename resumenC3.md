# Resumen C3 PDI
	(the last chance)

### Descripcion de objetos

Descriptores locales: Representaciones que describen caracter´ısticas
elementales de una vecindad reducida, parte de una regi´on de inter´es como:
forma, color, textura o movimiento. Ejemplo de descriptores: SIFT, SURF

### Puntos de interes
- Detector de esquinas de Harris: Se computa cada punto (x,y) de la imagen
 mediante la matriz M(x,y). Los valores propios λ1 y λ2  de M son
 proporcionales a las curvaturas principales de I. Regiones de la imagen se
 clasifican en:
	- uniforme (λ1 y λ2 pequeños),
	- region de borde (uno pequeño, uno grande)
	- region de esquina (ambos grandes).

	Para evitar computar explıcitamente λ1 y λ2, los autores propusieron un puntaje de esquina:

  $c(x, y) = λ1·λ2 − k·(λ1 +λ2)^2 = det(M(x, y))−k· [trace(M(x, y))]^2$

   Luego, se realiza non-aximum suppression a $V_8$ y son filtrados los
   candidatos con una respuesta de menos de una tasa θ de la respuesta m´axima
   encontrada.


- Good Features to Track de Shi-Tomasi

Sugieren usar un modelo de traslacion:


$$
\left(\begin{array}{cc}
x \\
y
\end{array}\right) =
\left(\begin{array}{cc}
t_x\\
t_y
\end{array}\right) +
\left(\begin{array}{cc}
a_{00} & a_{01} \\
a_{10} & a_{11}
\end{array}\right)
\left(\begin{array}{cc}
X' \\
Y'
\end{array}\right) =
\left(\begin{array}{cc}
t_x\\
t_y
\end{array}\right) +A
\left(\begin{array}{cc}
X'\\
Y'
\end{array}\right)
$$

En este caso, $A = M$ de Harris

### Scale Space

Teoria formal para manipular estructuras de imagenes a diferentes escalas,
representando una imagen como una familia de imagenes suavizadas con un
parametro (tamaño del filtro de suavizado). Para una imagen $f(x, y)$ , su
representacion de scale-space lineal (Gaussiana) es una familia de imagenes:

$L(x, y;t) = g(x, y;t) ∗ f (x, y)$

con:
      $g(x, y;t) = \frac{1}{2πt^{e−(x2+y2)/2t}}$

$t$ es la varianza del filtro Gaussiano. Si $t = 0$, $L(x, y; 0) = f (x, y)$.

En la medida que $t$ crece, $L$ es el resultado de suavizar $f$ con un filtro
cada vez mas grande, suprimiendo cada vez m´as detalles en la imagen.

### Puntos de interees - Hessiano rapido

Base del descriptor SURF, se basa en el determinante de la matriz Hessiana:

$$
H(x,y,σ) =
\left(\begin{array}{cc}
\frac{\partial^2 }{\partial^2 x}G(σ) * I(x,y) & \frac{\partial}{\partial x} \frac{\partial}{\partial y}G(σ) * I(x,y) \\
\frac{\partial}{\partial x} \frac{\partial}{\partial y}G(σ) * I(x,y) & \frac{\partial^2 }{\partial^2 y}G(σ) * I(x,y)
\end{array}\right)
$$

#### Imagenes integrales

Usada como forma rapida y efectiva para calcular sumas de pıxeles en una
imagen (escala de grises). Puede usarse para calcular la intensidad promedio
en la imagen. Para cada (x,y) se escribe la suma de todos los pıxeles arriba
y a la izquierda, incluyendo al de la posicion original:

![imagen de cuadritos](https://computersciencesource.files.wordpress.com/2010/09/adding_1.png)

![imagen de areas sumadas](https://computersciencesource.files.wordpress.com/2010/09/bigegsat.png)

![otro ejemplo](http://1.bp.blogspot.com/--riYD2Dg_Bo/Vczl_NjDw9I/AAAAAAAAA6w/ZbyPMPwR-FA/s1600/Integral_image.png)

## Puntos de interes - FAST

Detector de esquinas de alto rendimiento (FAST: Feature from Accelerated
Segment TEST), El algoritmo trabaja en un cırculo discretizado alrededor del
punto candidato p. P es esquina si existe un arco contiguo de al menos 9
pıxeles que sean mas brillantes o mas oscuros que p por un umbral T.

![fast image](fast.png)

## Descriptores locales de Caracteristicas

Luego de identificar los puntos de interes, se debe encontrar una
descripcion que se pueda usar para identificarlos entre imagenes.
Estos descriptores apuntan a capturar la textura de la vecindad local
y a ser invariantes a cambios de iluminacion, escala y rotacion.

#### Descriptores locales - Patch de imagen

La descripcion mas directa de un punto caracterıstico es la **vecindad** de
este (image patch). Para seguimiento, se asume que la apariencia del patch
casi no ha variado entre frames.

#### Descriptores locales - SURF

Inspirado en SIFT pero **es mas robusto** ante ciertas transformaciones. **Utiliza
Hessiano Rapido** .La idea de estos descriptores es encontrrar correspondencia
entre imagenes del mismo tipo.

Caracteristicas puntos de ineteres:
- Se usa hessiano rapido
- se aplica:
 $C(x,y,σ) = D_{xx}(σ)D_{yy}(σ) - (0.9D_{xy}(σ))^2 ≈ det[H(x,y,σ)]$
 donde D_{xx}, D_{yy}, D_{xy} resultan de convolucion entre imagen y los filtros de la imagen
- Factor 0.9 se agrega para mejorar aproximacion a $det[H(x, y, σ)]$ .
- Se aplica non-maximum suppression a vecindad de 3 × 3 × 3 y refinamiento
 subpixel
- Candidatos con $c < T$ (umbral) se rechazan
- SURF menos sensible al ruido

*Orientacion*: Respecto a la orientacion de las imagenes, es "inmune" a esta.
Se utilizan filtros de HAAR para generar una respuesta que es ponderada utilizando una Gaussiana segun distancia al punto de ineteres para todas kas muestras en
la vecindad circular.

Pasos del Descriptor (SURF):
- constru´ır region cuadrada centrada en el punto de interes y orientada
seguun la orientaci´on seleccionada previamente.
- Tamaño de la ventana de 20s.
- Region dividida en 4 × 4 sub-regiones cuadradas.
- Para cada sub-region, computar filtros de Haar en 5 × 5 puntos de
muestra equiespaciados.
- Respuestas horizontal dx y vertical dy , ponderadas por Gaussiana
centrada en punto de interes.
- Filtros Haar orientados seg´un orientaci´on de la region.
- Cada respuesta dx y dy es sumada sobre cada sub-region.
- Tambien se calculan |dx| y |dy| (polaridad de cambios de
intensidad). Entonces, cada sub-region representada por vector
descriptor $v$de 4D:
$$ v = (\sum d_{x}, \sum d_{y},\sum |d_{x}|, \sum |d_y|) $$

![fast image](SURF.png)

- Descriptor final se obtiene concatenando $v$ para todas las 4 × 4
sub-regiones (descriptor de largo 64).
- Filtros de Haar invariantes a offset de iluminacion.
- Invarianza al contraste normalizando el descriptor final a vector
unitario.


## Template Matching

Tecnica para encontrar areas de una imagen que coinciden (match; son similares)
a una imagen template (patch). Se necesita imagen fuente y template.

![template Matching](template.png)

Para identificar el area coincidente, es necesario comparar la imagen
template (T) con la imagen fuente (I), deslizandola: Mover el patch un pixel a la vez (de izquierda a derecha y de arriba a abajo). En cada posici´on, se calcula una
metrica para representar cuan bien o mal existe coincidencia (o cuan similar
es el patch a esa area en particular de la imagen fuente).

## Feature Matching

consiste en buscar un objeto o un conjunto de objetos en una imagen. Metodos
de base **KD-TREES** , metodos populares **BBF** y **FLANN**.

#### KD-Tress

K-dimensional tree es una a estructura de datos de particionamiento espacial para organizar puntos en un espacio k-dimensional. El kd-tree es un ´arbol binario
donde cada nodo es un punto k-dimensional. Cada nodo no hoja genera implicitamente un hiperplano de separacion que divide el espacio en dos (half-spaces). Los puntos a la izquierda de un hiperplano representados por sub-arbol izquierdo y los puntos a la derecha por sub-arbol derecho.

![kd-tree](kd-tree.png)

- **NN Search**: Busqueda de vecino mas cercano (Nearest Neighbor Search) en
kd-trees
	- Comenzando de la raız, descender por el arbol testeando a que lado de
	 cada nodo se encuentra.
	- Ir actualizando la distancia mas cercana en la medida que se avanza por
	  el arbol, hasta llegar a un nodo hoja.
	- Comenzar ascenso, chequeando si pueden haber puntos al otro lado del  	plano de corte del nodo actual. Se hace intersectando el hiperplano de corte con  una hiperesfera de radio la distancia actualmente mas cercana,
	centrada en el punto buscado:
		- Si hiperesfera cruza el plano (verificable en direccion de eje de
      corte), podrıa haber puntos mas cercanos al otro lado, y el
      algoritmo desciende por la otra rama (siguiendo procedimiento
      previo).
	  - Si no cruza, se continua ascenso, descartando la otra rama.
  - Termina cuando vuelve a la raız.

![nn-search](nn-search.png)

Como ayuda extra:
[Video de Kd-Tree](https://www.youtube.com/watch?v=Y4ZgLlDfKDg)

Complejidad:

| |promedio|peor caso|
| :------- | :-------: | :-------: |
|Construccion | $O(kn·log(n))$ | $O(kn·log(n))$ |
|Busqueda     | $O(log (n))$| $O(n)$ |

#### KD-Tress - Best Bin First (BBF)

Encuentra solucion aproximada a NN Search en espacios de muy alta dimensionalidad. Es un variante de kd-tree aproximado que retorna vecino mas cercano en la mayoria de los casos. Se diferencia de KD-Tree pues en el Ascenso (regreso) en el arbol se realiza mediante una cola de prioridad basada en cercania. BBF es mas rapido que kd-tree.

#### KD-Tress - FLANN

Algoritmo que utiliza los mejores dos algoritmos encontrados: **hierarchical k-means tree modificado** y **randomized kd-trees**.

- **Randomized KD-Tress**:
	- Se crean varios arboles
	- Se escoge la dimensi´on de corte aleatoriamente entre las primeras $D$
    dimensiones de mayor varianza.
	- En FLANN utilizan D = 5.
	- Para b´usqueda, se mantiene una cola de prioridad entre todos los
    arboles para que la b´usqueda se ordene seg´un proximidad de los bins.


- **Hierarchical k-means Tree**:
	- Dividir los puntos en cada nivel en K regiones mediante k-means clustering.
	- Continuar recursivamente para los puntos de cada region.
	- Detener recursi´on cuando n´umero de puntos en region menor que K

Existe una variante de este ultimo (revisar ppt).


## Flujo Optico

Estima movimiento entre dos imagenes consecutivas sin conocimiento previo. Los metodos de  ujo optico calculan el movimiento entre dos imagenes de una
secuencia, capturadas en tiempos $t-1$ y $t$.

- **Sparse optical flow**: Manera de especi car de antemano el subconjunto de
	puntos a seguir. La idea es obtener un seguimiento relativamente robusto y con able.

### Flujo Optico - Lucas Kanade (LK)

Es un metodo sparse, que se aplica a un subconjunto de puntos en la imagen y
se basa en informacion local. Esta informacion local es derivada de una pequeña ventana alrededor de cada punto de interes.

Algunos supuestos de (LK) :
- Brillo constante
- Persistencia Temporal
- Coherencia espacial.

####Funcionamiento (LK)

Persistencia Temporal (pequeños movimientos): Caso 1D: comenzando con ecuacion de consistencia en brillo, sustitur $f(x;t)$ por $I(x(t);t)$, y considerando dependencia implcita de $x$ sobre $t$ aplicar regla de la cadena de derivacion parcial:

$$
\frac{\partial I}{\partial x} \Big|_ t
(\frac {\partial x}{\partial t}) + \frac {\partial I}{\partial t} \Big|_ t = 0
$$

con $I_x$ derivada espacial en la primera imagen, $I_t$ derivada temporal
entre imagenes, y $v$ la velocidad buscada.

#### 1D -> 2D
