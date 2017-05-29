# Practica V - Distribucion Normal Multivariante
Xose Manuel Vilan Fragueiro  
27 de mayo de 2017  



---

#Introducción

Esta práctica puede consultarse en formato **html** en [**xvilan.github.io/practicasADM**](https://xvilan.github.io/practicasADM)

Se compone de dos ejercicios en los que se realiza un estudio de la distribución normal multivariante para muestras aleatorias y para el conjunto de datos de Dow Jones (paquete QRM) utilizando R. 

En probabilidad y estadística, una distribución normal multivariante, también llamada distribución gaussiana multivariante, es una generalización de la distribución normal unidimensional a dimensiones superiores.  

***

#Ejercicio [1] 

El primer ejercicio consiste en la construcción de representaciones gráficas de datos aleatorios pertenecientes a una distribución normal. El primer paso es definir el número de observaciones de la muestra.


```r
n = 250;
```

## (a) Univariante

Se crea un vector aleatorio X1, a partir de la distribución normal utilizando la función `rnorm` y se comprueban los primeros valores.


```r
X1 = rnorm(n, mean = 1, sd = sqrt(2));
head(X1);
```

```
[1]  2.7131278  1.6996293 -1.4321651 -0.1206419  0.9286292  1.9308860
```
\newpage
Se realiza el histograma de frecuencias relativas para comprobar gráficamente su forma.


```r
hist(X1, col = 'cornsilk', xlab = 'x1',
main = 'Simulacion N(m = 1, var = 2), n = 250', 
freq = FALSE, ylab = 'probabilidad',
font.lab = 2);
```

![](notebook_3_files/figure-html/unnamed-chunk-3-1.png)<!-- -->

Se comprueba, utilizando mediante la función `range`, que los valores están entre la media y tres veces su desviación típica, a ambos lados de la media. Es decir, que el 99,73% de los valores se encuentra en $\mu\pm 3 \sigma$. Se comprueba que el centro de la distribución se encuentra en el valor 1 como se había definido en su media.


```r
range(X1);
```

```
[1] -2.841432  5.021580
```
Por tanto, los valores x concuerdan con su distribución teórica $X -> N (\mu, \sigma)$.

##(b) Bivariante

Cargamos la librería *mvtnorm* de distribución normal multivariante y comprobamos en la cabecera de su descripción los parámetros que utiliza la función `rmvnorm`, 


```r
library(mvtnorm);
head(rmvnorm, n = 2);
```

```
                                                                       
1 function (n, mean = rep(0, nrow(sigma)), sigma = diag(length(mean)), 
2     method = c("eigen", "svd", "chol"), pre0.9_9994 = FALSE)         
```

\newpage
Se crea el vector de medias y de varianzas. La función `rmvnorm` necesita un vector de medias y otro de matriz de covarianzas. Devuelve una lista con una matriz de  $2 x 2$ y una matriz de $250 x 2$, de la que sólo se muestra su cabecera.


```r
n = 250;
(mean = c(1,2));
```

```
[1] 1 2
```

```r
(sigma = matrix(c(2,1,1,4), nrow = 2));
```

```
     [,1] [,2]
[1,]    2    1
[2,]    1    4
```

```r
X2 = rmvnorm(n, mean = c(1,2), sigma = matrix(c(2,1,1,4), nrow = 2));
head(X2);
```

```
           [,1]       [,2]
[1,]  1.3732935  3.0164902
[2,]  2.0157037  2.8875356
[3,] -0.6686842  1.4361330
[4,] -0.5897207  0.7945289
[5,] -0.5830052 -0.1781516
[6,]  3.1889082  8.0542993
```

Se pasa la lista X2 a un formato *data.frame* de R, se asignan nombres a las variables y se comprueba su estructura.


```r
X2 = data.frame(X2);

colnames(X2) = paste0('x',1:2);

str(X2); 
```

```
'data.frame':	250 obs. of  2 variables:
 $ x1: num  1.373 2.016 -0.669 -0.59 -0.583 ...
 $ x2: num  3.016 2.888 1.436 0.795 -0.178 ...
```

Se representa gráficamente la normal bivariada con la función `plot`, añadiendo al gráfico con la función `with` un punto cuadrado de color rojo que representa la media poblacional.


```r
library(car)
with(X2, plot(x1,x2, font.lab = 2, 
pch = 16, col = 'blue', cex = 0.7,
main = 'Simulación N2[c(1,2), matrix(c(2,1,1,4), nr = 2)], n = 250',
col.main = 'blue'));
points(1,2, pch = 15, cex = 2, col = 'red'); 
```

![](notebook_3_files/figure-html/unnamed-chunk-8-1.png)<!-- -->

En este primer gráfico no están concentrados todos los puntos entorno a la media, sino que parecen dispersos por la región central. Teóricamente cuanto más grande sea el tamaño muestral mejor replicamos el modelo poblacional y más concentrados entorno a la media estarán los valores.  

Seguidamente, se ejecuta el scatterplot y los histogramas de cada variable. Al gráfico de dispersión se añaden elipses que representan percentiles 50, 95 y 9.75 utilizando la función `dataEllipse`. Por definición, cada proyección (distribuciones marginales) deben comportarse como una normal univariante N(1,2) y N(2,2), debido a las propiedades de la distribución condicionada de la normal multivariante. Las marginales son las normales de los momentos correspondientes.

$$X1 | X2 = y \sim N_1(y,1)$$


```r
library(knitr)
dataEllipse(as.matrix(X2), levels = c(0.5,0.95,0.975), font.lab = 2, 
pch = 16, cex = 0.5, main = 'Simulación N2[c(1,2), matrix(c(2,1,1,4), nr = 2)], n = 250');
points(1,2,pch = 15, cex = 2);
```

![](notebook_3_files/figure-html/unnamed-chunk-9-1.png)<!-- -->

```r
with(X2, hist(x1, col = 'grey90', xlab = 'x1',
freq = FALSE, main = '', font.lab = 2,
ylab = 'probabilidad'));
```

![](notebook_3_files/figure-html/unnamed-chunk-9-2.png)<!-- -->

```r
with(X2, hist(x2, col = 'aliceblue', xlab = 'x2',
freq = FALSE, main = '', font.lab = 2,
ylab = 'probabilidad'));
```

![](notebook_3_files/figure-html/unnamed-chunk-9-3.png)<!-- -->

```r
kable(cbind(mean, sigma, round(cov2cor(sigma), digits = 4))); 
```



 mean                            
-----  ---  ---  -------  -------
    1    2    1   1.0000   0.3536
    2    1    4   0.3536   1.0000

Los histogramas son las proyectiones del grafico de dispersión sobre los ejes. El primero está centrada en 1 y el segundo está centrada en 2.  

Según el fráfico del vector de medias, no está claro, lo que está sucediendo con 250 puntos respecto a la normalidad de los datos.  

En la tabla final, se muestran los vectores de media y varianza poblacional, esta última contiene los valores de correlación (0.3536). Se deduce que es una correlación positiva, como también se observa en gráfico de dispersión por la dirección el semieje mayor de la elipse.  

Se vuelven a ejecutar los gráficos de este apartado, pero esta vez cambiando el tamaño de la muestral a 2000 valores procedentes de la normal.  



```r
library(car)

#tamaño muestral
n = 2000;

#vector de medias y varianza
mean = c(1,2);
sigma = matrix(c(2,1,1,4), nrow = 2); 
X2 = data.frame (rmvnorm(n, mean = c(1,2), sigma = matrix(c(2,1,1,4), nrow = 2)));  
colnames(X2) = paste0('x',1:2);

#Gráfico de dispersión con las elipses de percentiles 50, 95 y 9.75
dataEllipse(as.matrix(X2), levels = c(0.5,0.95,0.975), font.lab = 2, 
pch = 16, cex = 0.5, main = 'Simulación N2[c(1,2), matrix(c(2,1,1,4), nr = 2)], n = 2000');
points(1,2,pch = 15, cex = 2, col = 'red');
box(lwd = 2);
```

![](notebook_3_files/figure-html/unnamed-chunk-10-1.png)<!-- -->

```r
#Histrograma de la distribución marginal x1
with(X2, hist(x1, col = 'grey90', xlab = 'x1',
freq = FALSE, main = '', font.lab = 2,
ylab = 'probabilidad'));
```

![](notebook_3_files/figure-html/unnamed-chunk-10-2.png)<!-- -->

```r
#Histrograma de la distribución marginal x2
with(X2, hist(x2, col = 'aliceblue', xlab = 'x2',
freq = FALSE, main = '', font.lab = 2,
ylab = 'probabilidad'));
```

![](notebook_3_files/figure-html/unnamed-chunk-10-3.png)<!-- -->

```r
#Mismos valores de media y varianza poblacional.
```
En la simulación de tamaño muestral (n = 2000) se aprecia en el gráfico  claramente, cómo los puntos se agrupan alrededor de la media muestral y hay simetría.

Finalmente, otra forma de comprobar las propiedades de la simetría elíptica de la distribución normal bivariante, es representar cuatro gráficos de dispersión con distinto tamaño muestral en un solo grid. En el que se pueda comprobar como a medida que aumenta el tamaño muestral, se confirman las propiedades de la simetría. Al aumentar el tamaño muestral, nos acercamos al modelo poblacional y los puntos están más próximos al vector de media poblacional.


```r
mean = c(1,2);
sigma = matrix(c(2,1,1,4), nrow = 2);
kable(cbind(mean, sigma, round(cov2cor(sigma), digits = 4))); 
```



 mean                            
-----  ---  ---  -------  -------
    1    2    1   1.0000   0.3536
    2    1    4   0.3536   1.0000

```r
#tamaño muestral.
n = c(250, 500, 750, 1000);
library(car)
opar = par(no.readonly = TRUE);
par(mfrow = c(2,2), oma = c(2,2,3,2),
mar = c(4.5, 4.5, 1.5, 1.5)); 

#Bucle para generar el gráfico de dispersión con cada tamaño muestral
for (i in 1:length(n))
{
X2 = rmvnorm(n[i], mean = c(1,2), sigma = matrix(c(2,1,1,4), nrow = 2))
X2 = data.frame(X2)
colnames(X2) = paste0('x',1:2)

with(X2, plot(x1,x2, font.lab = 2, 
pch = 16, col = 'blue', cex = 0.4,
main = paste0('n = ',n[i]),
col.main = 'blue',
cex.main = 0.6));

points(1,2,pch = 15, cex = 2);
box(lwd = 2);

dataEllipse(as.matrix(X2), 
            plot.points = FALSE,
            levels = 0.15*1:6,  
            draw = TRUE,
            add = TRUE,
            xlim = range(X2[1,]),
            ylim = range(X2[,2]),
            grid = FALSE,
            lwd = 1.,
            center.pch = FALSE,
            col = 'black',
            lty = '1373');

if (i == 1)
{
mtext('Simulación N2[c(1,2), matrix(c(2,1,1,4), nr = 2)]',
col = 'brown', font = 2, outer = TRUE, line = .5,cex = 1.2); 
box('inner', lty = '1373', col = 'navy', lwd = 1.5); 
}
}
```

![](notebook_3_files/figure-html/unnamed-chunk-11-1.png)<!-- -->


##(c) Trivariante

En este caso se utilizan tres variables (Trinormal). Se repite el paso previo de construcción de la lista con vector de medias y varianza.


```r
n=250;
mean = c(0,3,-1);
sigma = matrix(c(2,1,1,1,4,0,1,0,5), nrow = 3);
X3 = rmvnorm(n, mean = c(0,3,-1), sigma = matrix(c(2,1,1,1,4,0,1,0,5), nrow = 3)); 
X3 = data.frame(X3);

colnames(X3) = paste0('x',1:3);

kable(head(X3));
```

        x1         x2           x3
----------  ---------  -----------
  2.654317   2.407172   -1.5039185
  2.071845   3.065572   -2.4188275
  1.462375   7.128782    0.0692148
 -1.522139   4.676177   -3.3386273
  2.467986   2.613454    4.4780246
  1.644707   6.551707    1.1596569

\newpage
Se genera un gráfico tipo scatterplot de forma matricial, 


```r
plot(X3, col = 'mediumseagreen', pch = 16, cex = 1.2, 
font.labels = 2, lwd = 2, gap = .25,
main = 'Simulación N2[c(0,3,-1), matrix(c(2,1,1,1,4,0,1,0,5), nr = 3)], n = 250'); 
```

![](notebook_3_files/figure-html/unnamed-chunk-13-1.png)<!-- -->

```r
kable(cbind(mean, sigma, round(cov2cor(sigma), digits  = 4))); 
```



 mean                                          
-----  ---  ---  ---  -------  -------  -------
    0    2    1    1   1.0000   0.3536   0.3162
    3    1    4    0   0.3536   1.0000   0.0000
   -1    1    0    5   0.3162   0.0000   1.0000

Se observa que X1 y X2 están correladas positivamente (r = 0.3536)por la dirección de la elipse que forman gráficamente, mientras que con X3 están incorreladas (r = 0). Esto también se puede comporbar en los valores de correlación de la tabla. 

Como complemento, se puede generar la misma nube de puntos, pero en lugar de hacerlo de forma bidimensional, en el que se muestren dispersiones de las variables dos a dos, realizado un gráfico tridimensional.


```r
library(scatterplot3d); 

scatterplot3d(as.matrix(X3), pch = 19, cex = 0.8,
color = 'seagreen', main = '3D Scatterplot del array X3, n=250', 
grid = F, cex.axis = 0.8, cex.lab = 1.5); 
```

![](notebook_3_files/figure-html/unnamed-chunk-14-1.png)<!-- -->

Y para un tamaño muestral mayor,

```r
#Definición del vector de tamaño 1000.
n=1000;
mean = c(0,3,-1);
sigma = matrix(c(2,1,1,1,4,0,1,0,5), nrow = 3);
X3 = data.frame(rmvnorm(n, mean = c(0,3,-1), 
sigma = matrix(c(2,1,1,1,4,0,1,0,5), 
nrow = 3))
); 
colnames(X3) = paste0('x',1:3);

#Gráfico tridimensional.
scatterplot3d(as.matrix(X3), pch = 19, cex = 0.8,
color = 'seagreen', main = '3D Scatterplot del array X3, n=1000', 
grid = F, cex.axis = 0.8, cex.lab = 1.5); 
```

![](notebook_3_files/figure-html/unnamed-chunk-15-1.png)<!-- -->

---

\newpage
#Ejercicio [2] 

En este ejercicio se discutirá la normalidad multivariante de los retornos diarios asociados a una serie de activos correspondientes al conjunto de datos del Dow Jones del paquete `QRM`. El objetivo es determinar gráficamente si los datos del DJ normales. 

En primer lugar, se cargan las librerías necesarias para obtener los datos.



Se genera un vector con las cabeceras de los campos. Se utiliza la función _"window"_ del paquete *timeseries* que permite extraer los **valores** para unas fechas dadas por el ejercicio.


```r
I = c('MO', 'KO', 'EK', 'HWP', 'INTC', 'MSFT', 'IBM', 'MCD', 'WMT', 'DIS');
data.0 = window(DJ[,I],'1992-01-01','1998-12-31');
data.0 = returns(data.0, method = 'discrete');
data.1 = 100*data.frame(data.0);
```

Se realiza un diagrama de nube de puntos. Una caracteristica curiosa de este tipo de datos, retornos diarios, es la "asimetría de las colas" que es observable gráficamente. 


```r
plot(data.1[10:1], upper.panel = NULL, 
main = 'Retornos diarios índice Dow Jones 1992-1998',
col = 'royalblue',
labels = colnames(data.1[10:1]), 
cex.labels = 1.5,
gap = .15);
```

![](notebook_3_files/figure-html/unnamed-chunk-18-1.png)<!-- -->

\newpage

```r
kable(round(sqrt(diag(cov(data.1))), digits = 4));
```



-----  -------
MO      1.7738
KO      1.4801
EK      1.7146
HWP     2.2456
INTC    2.3315
MSFT    2.0620
IBM     1.9161
MCD     1.5213
WMT     1.7756
DIS     1.7033
-----  -------

##Conclusión *dowjones*  

* A pesar de estar compuesto por 1.800 datos no presentan un comportamiento normal puro.

* La parte central de la matriz de gráficos se comporta de forma simétrica y parece que lo es, pero tiene datos muy lejanos al centro. Es decir, las colas de la distribución no lo son y por tanto los retornos no pueden ser normales.  

* Se da lo que se denomina problema del goteo, hay puntos que "gotean"" de la parte central. Por ejemplo, en el gráfico de dispersión WMT vs MO.

![](notebook_3_files/figure-html/unnamed-chunk-20-1.png)<!-- -->


* No son normales por que no se comporta de la misma manera que los datos aleatorios normales del ejercicio anterior. Hay patrones que contradicen lo anterior, como por ejemplo que los puntos están dispuestos hacia afuera.  

* Una manera de explicar comportamiento sería definirlo mediante una mixtura. Hay goteo, asimetría, pero una parte central en la que se podría modelar la densidad como una normal (pi) y la parte del gotteo como (1-pi). Podría incluirse una tercera componento para explicr la asimetría. No podemos calcularlo porque los parámetro son desconocidos.

---




  
  
