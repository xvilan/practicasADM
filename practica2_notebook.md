# Practica2
Xosé Manuel Vilán @xvilan  
12 de marzo de 2017  



#Introducción

Esta práctica consite en el análisis de componentes principales de tres conjuntos de datos (**ventas**, **sctkp**, **dowjones**). En estadística, el **Análisis de Componentes Principales** (en español *ACP*, en inglés, *PCA*) es una técnica utilizada para reducir la dimensionalidad de un conjunto de datos.

Técnicamente, el ACP busca la proyección según la cual los datos queden mejor representados en términos de mínimos cuadrados. Convierte un conjunto de observaciones de variables posiblemente correlacionadas en un conjunto de valores de variables sin correlación lineal llamadas componentes principales.

Los objetivos del Análisis de Componentes Principales son:  

* Resumir la variación total en una __dimensión menor__.  
* Identificar fuentes de variación __interpretables__.  
* Explorar características __inesperadas__ de los datos.  

El ACP se emplea sobre todo en análisis exploratorio de datos y como herramienta de procesado inicial de los datos. El ACP comporta el cálculo de la descomposición en autovalores y autovectores de la matriz de covarianza, normalmente tras centrar los datos en la media de cada atributo. Las componentes principales están basadas en combinaciones lineales de los datos originales centrados $X-\overline{x}$  

***  
> Nota

Esta página web está escrita usando Markdown y es un [R Notebook](http://rmarkdown.rstudio.com). El notebook está creado con **R-Studio**, que utiliza *knitr* y *pandoc* para exportar el código a multiples formatos, entre ellos *.md*. Utilizando el botón *Code* situado en la parte superior derecha es posible descargar el archivo *.Rmd* y ejecutar los scripts mediante R-Studio desktop.  

***

#Carga de datos

En primer lugar, se carga el archivo [**ventas.txt**](./ventas.txt)  a R-Studio. Debemos indicar previamente a R-Studio, cuál es el directorio de trabajo en el que estará localizado el archivo. 


```r
ventas = read.table('ventas.txt')
show(ventas)
```

```
##          ventas beneficios activos
## G.M.       26.7        3.3    15.8
## Exxon      38.4        2.4    19.5
## Ford       19.2        1.7     8.4
## Mobil      20.6        1.0     8.2
## Texaco     18.9        0.9     9.4
## Std.Oil    14.8        1.0     7.6
## IBM        19.0        2.7    12.6
## Gulf       14.2        0.8     7.3
## G.E.       13.7        1.1     5.9
## Chrysler    7.7        0.2     2.9
```
  
Para calcular el número de filas se puede emplear la función _"nrow"_.  


```r
n = nrow(ventas)
print(c('número de filas=',n))
```

```
## [1] "número de filas=" "10"
```

***  

#Descripción de los datos  


Se crea el vector de **medias muestrales _m_**, redondeando a cuatro decimales. El vector de medias muestrales, dada una matriz de datos $X_{n \times p} = (x_{ij})$, el vector de medias muestrales se calcula como:  
  
$\overline{x} = \frac{1}{n} X^T 1_n = (\overline{x_j}:j=1,...,p)$ 

```r
m = round(colMeans(ventas), digits = 4); 
round(m, digits = 4);
```

```
##     ventas beneficios    activos 
##      19.32       1.51       9.76
```

y la matriz de **covarianzas muestrales _S_** , que se calcula como:  
  
$S = \frac{1}{n} X^TX-\overline{x} \overline{x}^T = (s_{jk}:j=1,...,p)$  

```r
S = cov(ventas); 
round(S, digits = 4);
```

```
##             ventas beneficios activos
## ventas     70.4107     5.8731 39.0653
## beneficios  5.8731     0.9699  4.1149
## activos    39.0653     4.1149 24.0560
```

Se calcula la **Variación Total**, utilizando la función $VT = traza(S)$, en la que se suma la diagonal principal de la matriz de covarianzas muestrales,


```r
vt.ventas= round(sum(diag(S)), digits = 4)
vt.ventas
```

```
## [1] 95.4366
```

Para el cálculo posterior de las variables tipificadas, se calcula la raiz de la diagonal principal de matriz de covarianzas,


```r
round(sqrt(diag(S)), digits = 4)
```

```
##     ventas beneficios    activos 
##     8.3911     0.9848     4.9047
```

Ahora calculamos la **matriz de correlación _R_**,cuya diagonal principal será de unos. Si $D = diag(S)$ y $d = diag(D^{^-1/_2})$ entonces la matriz de correlaciones se calcularía como:  
  
    
$R = D^{^-1/_2}\times S \times D^{^-1/_2} = S \times dd^T = (r_{jk}=\frac{s_{jk}}{\sqrt{s_{jj}s_{kk}}})$   


```r
R = cor(ventas); 
round(R, digits = 4)
```

```
##            ventas beneficios activos
## ventas     1.0000     0.7107  0.9492
## beneficios 0.7107     1.0000  0.8519
## activos    0.9492     0.8519  1.0000
```

Se calculan las **correlaciones mínima y máxima**. Para la máxima  previamente se resta una matriz identidad de tamaño **n**, para que no se utilicen los valores iguales a uno,


```r
round(c(min(R),max(R - diag(ncol(ventas)))), digits = 4);
```

```
## [1] 0.7107 0.9492
```

##Gráfico

Finalmente, en este apartado de descripción de los datos, se genera un gráfico de tipo **Matriz de Nube Puntos** o *scatter plot matrix* usando la función *"plot* con unos determinados parámetros opcionales de salida gráfica.


```r
plot(ventas,pch = 19,cex = 1,upper.panel = NULL,col = 'blue', 
main = 'Datos de Ventas',cex.main = 1.4,gap = .15,cex.labels = 2,font.labels = 2)
```

![](practica2_notebook_files/figure-html/unnamed-chunk-9-1.png)<!-- -->
  
***  

#Componentes principales 

El software R-Studio incluye en el paquete *stats* la función *"princomp"*, que se utiliza en el análisis de componentes principales. Toma como argumento la matriz de datos y devuelve un vector con siete elementos como respuesta:

Elemento     |Descripción
-------------|-----------
$sdev        |Las desviaciones estándar de las componentes principales.
$loadings    |Matiz de autovectores y matriz de proporciones
$center      |Los valores medios que se restaron
$scale       |Las escalas aplicadas a cada variable
$n.obs       |El número de observaciones
$scores      |Matriz de componentes principales (si es calculable)
$call        |Llamada al nombre de la matriz de datos

Puede consultarse la ayuda de esta función usando:

```r
help("princomp");
```

```
## starting httpd help server ...
```

```
##  done
```
  
  
Se, por tanto, este vector de **componentes principales** haciendo que muestre todos los elementos de la respuesta,

```r
pc = princomp(ventas) 
pc[1:7]
```

```
## $sdev
##    Comp.1    Comp.2    Comp.3 
## 9.1590678 1.3675840 0.3661852 
## 
## $loadings
## 
## Loadings:
##            Comp.1 Comp.2 Comp.3
## ventas      0.866  0.485 -0.122
## beneficios        -0.372 -0.925
## activos     0.494 -0.792  0.359
## 
##                Comp.1 Comp.2 Comp.3
## SS loadings     1.000  1.000  1.000
## Proportion Var  0.333  0.333  0.333
## Cumulative Var  0.333  0.667  1.000
## 
## $center
##     ventas beneficios    activos 
##      19.32       1.51       9.76 
## 
## $scale
##     ventas beneficios    activos 
##          1          1          1 
## 
## $n.obs
## [1] 10
## 
## $scores
##               Comp.1     Comp.2      Comp.3
## G.M.       9.5129590 -1.8714091 -0.38928000
## Exxon     21.4042517  1.2035355  0.34001642
## Ford      -0.7609231  0.9480711 -0.64981325
## Mobil      0.2988297  2.0451158 -0.24556489
## Texaco    -0.5886351  0.3082677  0.48639468
## Std.Oil   -5.0209125 -0.2906013  0.24917581
## IBM        1.2172603 -2.8461707 -0.04110956
## Gulf      -5.7041718 -0.2694851  0.39987677
## G.E.      -6.8054807  0.4852114 -0.31953403
## Chrysler -13.5531775  0.2874647  0.16983805
## 
## $call
## princomp(x = ventas)
```

Para el cálculo de **varianzas _var_** ($\sigma^2$) se puede utilizar el resultado de las desviaciones estandard, indicando el nombre del elemnto elevado al cuadrado, de la siguiente forma:


```r
var = with(pc, sdev^2)
round(var, digits = 4)
```

```
##  Comp.1  Comp.2  Comp.3 
## 83.8885  1.8703  0.1341
```

Se genera la variable _sc_ para utilizarlo en Análisis equivalentes, si se sustituye $S_c = \frac{n}{n-1}*S$ cuasi-varianzas.


```r
sc = 1 - (1/n)
```
De forma que la varianza equivalente y, por tanto, los **Autovalores _var.eq_** ($L_{p \times 1}$) son:


```r
var.eq = (1/sc)*var
round(var.eq, digits = 4)
```

```
##  Comp.1  Comp.2  Comp.3 
## 93.2095  2.0781  0.1490
```

Para calcular los coeficientes de los **Autovectores _G_** ($G = (g_1,g_2,...,g_p)$) (transpuesta), 


```r
G = with(pc, loadings)[,]
round(G, digits = 4)
```

```
##            Comp.1  Comp.2  Comp.3
## ventas     0.8661  0.4846 -0.1225
## beneficios 0.0772 -0.3717 -0.9251
## activos    0.4939 -0.7918  0.3594
```

Y para obtener la **matriz de componentes principales _scores_**:

$Y = (X - 1_n \overline{x}^T)\times G$,  


```r
scores = data.frame(with(pc, scores));
show(scores)
```

```
##               Comp.1     Comp.2      Comp.3
## G.M.       9.5129590 -1.8714091 -0.38928000
## Exxon     21.4042517  1.2035355  0.34001642
## Ford      -0.7609231  0.9480711 -0.64981325
## Mobil      0.2988297  2.0451158 -0.24556489
## Texaco    -0.5886351  0.3082677  0.48639468
## Std.Oil   -5.0209125 -0.2906013  0.24917581
## IBM        1.2172603 -2.8461707 -0.04110956
## Gulf      -5.7041718 -0.2694851  0.39987677
## G.E.      -6.8054807  0.4852114 -0.31953403
## Chrysler -13.5531775  0.2874647  0.16983805
```

Es necesario cambiar al signo tanto de **_G_** como de **_scores_**. A continuación, se muestra para la primera componente:
 

```r
G[,1] = -G[,1];
round(G[,1], digits =  4)  
```

```
##     ventas beneficios    activos 
##    -0.8661    -0.0772    -0.4939
```


```r
scores[,1] = -scores[,1]
round(scores[,1], digits =  4) 
```

```
##  [1]  -9.5130 -21.4043   0.7609  -0.2988   0.5886   5.0209  -1.2173
##  [8]   5.7042   6.8055  13.5532
```

El siguiente paso será comprobar que la matriz de componentes principales cumple con las propiedades del teorema,

* El vector de **medias muestrales _m.cp_**  ($\overline{y}$) es igual a cero.  
* La matriz de **covarianzas muestrales _S.cp_** ($S_y$) tiene como diagonal el vector de autovalores.
* Las componentes son **incorreladas** de **media cero** y **decreciente de variabiliad**.


```r
m.cp = round(colMeans(scores), digits = 4)
m.cp
```

```
## Comp.1 Comp.2 Comp.3 
##      0      0      0
```


```r
S.cp= round(cov(scores), digits =  4)
S.cp
```

```
##         Comp.1 Comp.2 Comp.3
## Comp.1 93.2095 0.0000  0.000
## Comp.2  0.0000 2.0781  0.000
## Comp.3  0.0000 0.0000  0.149
```

Otra comprobación a realizar sobre la matriz de componentes principales es, calcular la **Variación Total _VT.cp_**, que también debe coincidir con la VT de los datos:  


```r
VT.cp = round(sum(diag(cov(scores))),digits = 4)
VT.cp
```

```
## [1] 95.4366
```

##Gráfico Scatter plot

Se genera un gráfico de Matriz de nube puntos (scatter plot matrix). Para ello, en primer lugar se extrae una variable *r* que es la dimensión de la matriz de componentes principales, en segundo lugar se utiliza la función *plot* con una serie de parámetros.

```r
r = with(scores, range(Comp.1));

plot(scores, pch = 19, cex = 1.2, upper.panel = NULL, col = 'royalblue', main = 'ACP de Ventas', cex.main = 1.3,xlim = r, 
ylim = r,gap = .15,cex.labels = 1.5,font.labels = 2)
```

![](practica2_notebook_files/figure-html/unnamed-chunk-22-1.png)<!-- -->

Puede observarse en el gráfico, que prácticamente todos los valores se encuentran sobre una línea horizontal

##Gráfico Coordenadas esféricas


```r
plot(scores/sqrt(var), pch = 19,cex = 1.2, upper.panel = NULL, col = 'mediumseagreen', main = 'Datos esféricos de Ventas',labels = paste0('esf.',c(1:5)),cex.main = 1.3, gap = .15,cex.labels = 1.5,
font.labels = 2)
```

![](practica2_notebook_files/figure-html/unnamed-chunk-23-1.png)<!-- -->


***
#Reducción de la dimensión

En este apartado se trabaja con el objetivo del ACP, la reducción de la dimensión para realizar el resumen de los datos.
En primer lugar, se calcula la proporción de variación y proporción acumulada, mostrándose luego en una única matriz de variación explicada.


```r
prop = S.cp/VT.cp; 
acum = cumsum(prop)
table = round(data.frame(S.cp,prop,acum, row.names =NULL ),digits = 4)
cat('', sep = '\n');
```

**Proporciones**

```r
prop
```

```
##           Comp.1     Comp.2      Comp.3
## Comp.1 0.9766641 0.00000000 0.000000000
## Comp.2 0.0000000 0.02177467 0.000000000
## Comp.3 0.0000000 0.00000000 0.001561246
```
**Proporciones acumuladas**

```r
acum
```

```
## [1] 0.9766641 0.9766641 0.9766641 0.9766641 0.9984388 0.9984388 0.9984388
## [8] 0.9984388 1.0000000
```
**Matriz de proporción explicada**

```r
show(table)
```

```
##    Comp.1 Comp.2 Comp.3 Comp.1.1 Comp.2.1 Comp.3.1   acum
## 1 93.2095 0.0000  0.000   0.9767   0.0000   0.0000 0.9767
## 2  0.0000 2.0781  0.000   0.0000   0.0218   0.0000 0.9767
## 3  0.0000 0.0000  0.149   0.0000   0.0000   0.0016 0.9767
## 4 93.2095 0.0000  0.000   0.9767   0.0000   0.0000 0.9767
## 5  0.0000 2.0781  0.000   0.0000   0.0218   0.0000 0.9984
## 6  0.0000 0.0000  0.149   0.0000   0.0000   0.0016 0.9984
## 7 93.2095 0.0000  0.000   0.9767   0.0000   0.0000 0.9984
## 8  0.0000 2.0781  0.000   0.0000   0.0218   0.0000 0.9984
## 9  0.0000 0.0000  0.149   0.0000   0.0000   0.0016 1.0000
```

##Gráfico de pendiente (Screen plot)


```r
plot(pc, type = 'l', col = 'royalblue', 
pch = 19, lwd = 2.5, cex = 1.5, 
main = 'Gráfico de pendiente de autovalores',
font.lab = 2,
cex.main = 1.3)
```

![](practica2_notebook_files/figure-html/unnamed-chunk-28-1.png)<!-- -->
La **reducción de la dimensión** sería de $q = 1$, ya que $Y1$ explica el 97,67% de la variación.

***
#Interpretación del análisis

**Y1:  coeficientes constantes (aproximadamente)**
variación proporcional a nota media  
  
$Y1 =  -0.8661X_1-0.0772X_2-0.4939X_3- $   

$\frac{X1+X2+X3}{3}$

**1/3 normalizado:**


```r
1/sqrt(3)
```

```
## [1] 0.5773503
```
***

```r
cons = -colMeans(ventas) %*% G[,1]; 
colnames(cons) = 'cons';
cat('', sep = '\n'); round(cons[,], d = 4);
```

```
##    cons 
## 21.6698
```


```r
cat('', sep = '\n'); 
```

```r
mat<-data.frame(pc.1 = round((1/sqrt(3))*(scores[,1] - cons), digits = 4), n.media = rowMeans(ventas))  

cat('', sep = '\n'); 
```

```r
round(G[,2], digits = 4)
```

```
##     ventas beneficios    activos 
##     0.4846    -0.3717    -0.7918
```

```r
show(mat)
```

```
##              pc.1   n.media
## G.M.     -18.0034 15.266667
## Exxon    -24.8688 20.100000
## Ford     -12.0718  9.766667
## Mobil    -12.6836  9.933333
## Texaco   -12.1712  9.733333
## Std.Oil   -9.6123  7.800000
## IBM      -13.2139 11.433333
## Gulf      -9.2178  7.433333
## G.E.      -8.5819  6.900000
## Chrysler  -4.6862  3.600000
```

**Y2: Comparación comportamiento libro abierto - cerrado** 


```r
round(G[,3], digits = 4)
```

```
##     ventas beneficios    activos 
##    -0.1225    -0.9251     0.3594
```


> **Conclusión**:Conservar únicamente Y1

***

**Matriz de correlación** ( Ventas ; Y1 )

Matriz de correlación entre los datos y las tres columnas de la matriz de componentes principales.

```r
cat('', sep = '\n'); round(cor(data.frame(ventas,scores[,1:2]))[1:3,1:3], digits = 4) 
```

```
##            ventas beneficios activos
## ventas     1.0000     0.7107  0.9492
## beneficios 0.7107     1.0000  0.8519
## activos    0.9492     0.8519  1.0000
```

## Gráfico ACP

Diseño

```r
plot(scores, pch = 19, 
col = 'mediumseagreen', cex = 1.4, 
xlim = r, ylim = r);

#lineas de referencia
abline(h = 0, v = 0, lty = '1373', col = 'black',lwd = 1);

#identificar individuos
lab = c(1:n); 
text(scores, labels = lab, font = 2, col = 'navy',cex = 1.5, pos = 1) 
```

![](practica2_notebook_files/figure-html/unnamed-chunk-34-1.png)<!-- -->
Gráfico final

```r
#seleccionar individuos
lab.plot = rep('',n);  
lab.plot[1] = 'Std.1   '
lab.plot[c(2,28,66,81)] = paste0('Std.',c(2,28,66,81));

# vectorizar cex, pch, col, y pos 
cex.plot = rep(1.4,n);
cex.plot[c(1,2,28,66,81)] = 1.6;
pch.plot = rep(19,n);
pch.plot[c(1,2,28,66,81)] = 15;
col.plot = rep('mediumseagreen',n);
col.plot[c(1,2,28,66,81)] = 'orangered';
pos.plot = rep(1,n); 
pos.plot[1] = 3;

plot(scores, pch = pch.plot, col = col.plot, cex = cex.plot, xlim = r, ylim = r, col.lab = 'royalblue', font.lab = 2, cex.lab = 1.2)  ;
#box(lwd = 2);

#  líneas de referencia
abline(h = 0, v = 0, lty = '1373', col = 'brown',lwd = 2) 
points(.0,.0,pch = 16, cex = 1.2, col = 'firebrick1')

# identificar individuos 
text(scores, labels = lab.plot, font = 2, col = 'navy', cex = 1., pos = pos.plot) 

title('ACP de Ventas', col.main = 'Blue',cex.main = 1.2)
```

![](practica2_notebook_files/figure-html/unnamed-chunk-35-1.png)<!-- -->
Vuelta a ventas

```r
cat('',sep = '\n'); round(G[,1:2], digits = 4);
```

```
##             Comp.1  Comp.2
## ventas     -0.8661  0.4846
## beneficios -0.0772 -0.3717
## activos    -0.4939 -0.7918
```

```r
cat('',sep = '\n'); ventas[c(1,2), ]
```

```
##       ventas beneficios activos
## G.M.    26.7        3.3    15.8
## Exxon   38.4        2.4    19.5
```





