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