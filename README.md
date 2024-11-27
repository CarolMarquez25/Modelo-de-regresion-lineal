#  Modelo de regresión lineal 

Este es un análisis de regresión lineal. Puedes ver el reporte completo [aquí](https://rpubs.com/carol_marquez/modeloderegresionlineal).

---
 ## **Carol Márquez**
 
 29/11/2022
---

# MRL del volumen de madera en un aserradero

**Formula:** $y= \beta 0+ \beta 1+ e$

donde:

$y=$ La variable respuesta o dependiente

$x=$ La variable regresora o independiente

$\beta 0 =$ La ordenada al origen

$\beta 1 =$La pendiente

$e =$ El error

## Ejemplo:

# Distribución del volumen de madera en un aserradero

Para el ejemplo se utiliza una base de datos sobre medidas de árboles de los que se obtuvo madera en un aserradero.

**Descripción de las variables**

*HT* = altura en pies. Variable independiente

*DBH* = diámetro del tronco a 4 pies (1.22 metros de altura en pulgadas). Variable independiente

*D16* = diámetro del tronco a 16 pies (4.8768 metros de altura en pulgadas). Variable independiente

*VOL* = volumen de madera obtenida (en pies cúbicos). Variable dependiente

```{r}
## Librerias 
 
 library(zoo)
 library(xts)
 library(readr)
 library(PerformanceAnalytics)
 library(lmtest)
library(carData)
library(car)
```

```{r}

Arboles <- read_csv("C:/Users/Carol/Documents/base arboles.csv")
head(Arboles)
Arboles<-Arboles[,-1]
```

**Correlación entre las variables**

```{r}
chart.Correlation(Arboles)
```

Se puede observar una alta correlación entre el volumen de madera obtenida con las demás variables. Sin embargo, también hay alta correlación entre las variables, lo que presenta puede presentar problemas de multicolinealidad.

Se procede a realizar la propuesta del modelo.

```{r}
 ####Ajustar Modelo de regresión lineal
 
 modelo <- lm(VOL ~ DBH + D16+ HT, data = Arboles)
 modelo
summary(modelo) 
```

**Planteamiento de Hipótesis:**

$H0$: El modelo No se justa de manera lineal

$Ha$: El modelo se ajusta de manera lineal

El p valor\< 0.05. Por lo tanto, se rechaza la H0 y se dice que el modelo si se ajusta de manera líneal a los datos.

El valor de R cuadrado: 0.8776, nos dice que de la variabilidad total del volumen de madera obtenida de los árboles, el 87.76% esta explicada por la altura, el diámetro del tronco a 4 pies y el diámetro del tronco a 16 pies.

***Análisis de varianza***

```{r}
anova<-anova(modelo)
anova
```

Se observa que todas las variables son significativas.

*Intervalos de confianza*

Se muestran los intervalos a un 95% de confianza.

```{r}
confint(modelo, level=0.95)
```

# Validación de los supuestos

***Normalidad***

```{r}
##Histograma de los residuales
e<-modelo$residuals
hist(e)
qqnorm(e)
qqline(e, col="red")
```

Planteamiento de Hipótesis:

$h0$: Los errores se distribuyen de manera normal.

$h1$: Los errores no se distribuyen de manera normal.

```{r}
##Test de Shapiro Wilk para probar normalidad

shapiro.test(e)
```

El p-value es 0.2691. No se rechaza la H0, ya que p_valor\> 0.05. Por lo tanto, no se rechaza $h0$ y se dice que los valores se distribuyen de manera normal.

**Homogeneidad de varianza**

```{r}
plot(e~modelo$fitted.values)
```

```{r}
##prueba de Breush pagan
bptest(modelo)
```

Planteamiento de hipótesis

$h0$: Existe homogeneidad de varianza

$h1$: No existe homogeneidad de varianza

El p-value = 0.01584. No se rechaza la $h0$, ya que p_valor\> 0.05. Por lo tanto no se rechaza la $h0$ y se dice que existe homogeneidad de varianza.

**Independencia**

```{r}
plot(ts(e))
abline(h=0, col="red")
```

Planteamiento de hipótesis:

$ho$: No hay autocorrelación entre los errores o son independientes.

$ha$: Hay autocorrelación entre los errores o NO son independientes.

Prueba de Durbin Watson para probar independencia

```{r}
dwt(modelo, alternative ="two.sided")

```

El p-value = 0.03. No se rechaza la H0, ya que p_valor\> 0.05. Por lo tanto, no se rechaza la $h0$ y se dice que no hay autocorrelación entre los errores o son independientes.

# Conclusión

Se concluye que obtuvimos un buen modelo, con una precisión del 87.76% explica el volumen de madera obtenida de los árboles y cumple con los tres supuestos requeridos, por lo tanto, es apto para estimar nuevas predicciones.
