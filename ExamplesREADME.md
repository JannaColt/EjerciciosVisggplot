Más de gráficos 3D

```R
# Adding Titles and Labeling Axes to Plot
cone <- function(x, y){
    sqrt(x ^ 2 + y ^ 2)
}

# prepare variables.
x <- y <- seq(-1, 1, length = 30)
z <- outer(x, y, cone)

# plot the 3D surface
# Adding Titles and Labeling Axes to Plot
persp(x, y, z,
      main="Perspective Plot of a Cone",
      zlab = "Height",
      theta = 30, phi = 15,
      col = "orange", shade = 0.4)



# (1) The Obligatory Mathematical surface.
#     Rotated sinc function.

x <- seq(-10, 10, length= 30)
y <- x
f <- function(x, y) { r <- sqrt(x^2+y^2); 10 * sin(r)/r }
z <- outer(x, y, f)
z[is.na(z)] <- 1
op <- par(bg = "white")
persp(x, y, z, theta = 30, phi = 30, expand = 0.5, col = "lightblue")
persp(x, y, z, theta = 30, phi = 30, expand = 0.5, col = "lightblue",
      ltheta = 120, shade = 0.75, ticktype = "detailed",
      xlab = "X", ylab = "Y", zlab = "Sinc( r )"
) -> res
round(res, 3)

# (2) Add to existing persp plot - using trans3d() :

xE <- c(-10,10); xy <- expand.grid(xE, xE)
points(trans3d(xy[,1], xy[,2], 6, pmat = res), col = 2, pch = 16)
lines (trans3d(x, y = 10, z = 6 + sin(x), pmat = res), col = 3)

phi <- seq(0, 2*pi, len = 201)
r1 <- 7.725 # radius of 2nd maximum
xr <- r1 * cos(phi)
yr <- r1 * sin(phi)
lines(trans3d(xr,yr, f(xr,yr), res), col = "pink", lwd = 2)
## (no hidden lines)

# (3) Visualizing a simple DEM model

z <- 2 * volcano        # Exaggerate the relief
x <- 10 * (1:nrow(z))   # 10 meter spacing (S to N)
y <- 10 * (1:ncol(z))   # 10 meter spacing (E to W)
## Don't draw the grid lines :  border = NA
par(bg = "slategray")
persp(x, y, z, theta = 135, phi = 30, col = "green3", scale = FALSE,
      ltheta = -120, shade = 0.75, border = NA, box = FALSE)

# (4) Surface colours corresponding to z-values

par(bg = "white")
x <- seq(-1.95, 1.95, length = 30)
y <- seq(-1.95, 1.95, length = 35)
z <- outer(x, y, function(a, b) a*b^2)
nrz <- nrow(z)
ncz <- ncol(z)
# Create a function interpolating colors in the range of specified colors
jet.colors <- colorRampPalette( c("blue", "green") )
# Generate the desired number of colors from this palette
nbcol <- 100
color <- jet.colors(nbcol)
# Compute the z-value at the facet centres
zfacet <- z[-1, -1] + z[-1, -ncz] + z[-nrz, -1] + z[-nrz, -ncz]
# Recode facet z-values into color indices
facetcol <- cut(zfacet, nbcol)
persp(x, y, z, col = color[facetcol], phi = 30, theta = -30)

par(op)
# }
```


# Ejercicios de visualización con Ggplot2

[![CRAN_Status_Badge](http://www.r-pkg.org/badges/version/ggplot2)](http://cran.r-project.org/package=ggplot2)

<!-- [![Extensiones](https://exts.ggplot2.tidyverse.org/gallery/)] -->


## Toolbox y sintaxis
### Ejercicios con Geoms

Creamos un dataframe
```R
df <- data.frame(

    x = c(5, 4, 8),

    y = c(1, 6, 9),

    label = c("a","b","c")
)
```

Luego la primera capa del gráfico

```R
p <- ggplot(df, aes(x, y, label = label)) +

    labs(x = NULL, y = NULL) + # esconder etiqueta 

    theme(plot.title = element_text(size = 12)) 
```

y  ahora probamos los distintos tipos básicos de geom

```R
p + geom_point() + ggtitle("point")

p + geom_text() + ggtitle("text")

p + geom_bar(stat = "identity") + ggtitle("bar")

p + geom_tile() + ggtitle("raster")

p + geom_line() + ggtitle("line")

p + geom_area() + ggtitle("area")

p + geom_path() + ggtitle("path")

p + geom_polygon() + ggtitle("polygon")
```

algunas sugerencias con los data frames disponibles en el paquete

[Raw Data](https://github.com/JannaColt/ggplot2/tree/master/data-raw)


```R
ggplot(iris, aes( Species, Sepal.Width)) + geom_bar(stat = "identity")

ggplot(iris, aes(Sepal.Width)) + geom_histogram(bins = 20)

ggplot(iris, aes(Sepal.Width, Sepal.Length, color = Species)) + geom_point()

ggplot(iris, aes( Species, Sepal.Width)) + geom_boxplot()

ggplot(faithful, aes(x=eruptions, y=waiting)) + geom_point()

ggplot(diamonds, aes(x=depth)) + geom_histogram(binwidth=0.1)

g <- ggplot(mpg, aes(class)) + geom_bar()
g + geom_bar(aes(fill = manufacturer))

 ggplot(diamonds, aes(cut, price)) + geom_boxplot()
 
ggplot(diamonds, aes(carat, price)) + geom_point() 

ggplot(diamonds, aes(carat, price)) + geom_point(alpha=1/3) 

ggplot(mtcars, aes(mpg, wt)) + geom_point() + geom_line(color="blue")

ggplot(mtcars, aes(mpg, wt)) + geom_point() + geom_path(color="blue")

ggplot(mtcars, aes(wt, mpg)) + geom_point() + geom_path(color="blue")


```
¿Cómo cambia Hwy frente a la clase en el df mpg?
¿En estas clases como se ve el tipo drv?
¿Cual sería la media?

```R
ggplot(mpg, aes(class, hwy)) + geom_boxplot()
ggplot(mpg, aes(class, hwy, fill = drv)) + geom_boxplot()
ggplot(mpg, aes(class, hwy, fill = drv)) + geom_boxplot() + geom_point()

```
#### Geoms colectivos, múltiples grupos en un objeto geom y en múltiples capas

Usando los geom colectivos, usualmente cuando el default no funciona, tenemos que definir exactamente los grupos. Nos sirve además cuando necesitamos que se desplieguen 
otros elementos como estadísticos en el mismo objeto geométrico

```R
library(nlme) # Si el paquete no esta, hay que instalarlo primero
View(Oxboys)
ggplot(Oxboys, aes(age, height)) + geom_point() +
+     geom_line()

```
Debido a la estructura de los datos (observaciones longitudinales, un sujeto a través de diferentes tiempos, las líneas quedan sin sentido alguno, para resolver la 
visualización hay que definir el grupo, en el caso de este dataset sería dependiendo del sujeto

```R
ggplot(Oxboys, aes(age, height, group = Subject)) + geom_point() +
+     geom_line()
```
Si un grupo no se encuentra definido por una sola variable, si no por múltiples variables se usa *interaction* (dentro del aes *Group*).

En otras ocasiones queremos graficar resúmenes a diferentes grados de agregación (usualmente estos son estadísticos o modelos), por ejemplo si en el gráfico anterior queremos observar tendencia lineal, podemos agregar una línea. Para ello usamos otro **Geom**, denominado *geom_smooth*.

```R
ggplot(Oxboys, aes(age, height, group = Subject)) + geom_line() + geom_point() +
geom_smooth(method = "lm", se = FALSE)
```
Lo anterior agrega una línea ajustada a cada uno de los sujetos, sin embargo, queremos una línea de tendencia para todas las líneas.

```R
ggplot(Oxboys, aes(age, height)) + geom_line(aes(group = Subject)) + geom_point() +
+     geom_smooth(method = "lm", se = TRUE)
```


Cuando tratamos con múltiples estéticas tenemos que considerar algunos puntos, para el caso de líneas y paths se aplica la estética por segmentos

```R
df <- data.frame(x = 1:3, y = 1:3, colour = c(1,3,5))

ggplot(df, aes(x, y, colour = factor(colour))) + geom_line(aes(group = 1), size = 2) + geom_point(size = 5)
ggplot(df, aes(x, y, colour = colour)) + geom_line(aes(group = 1), size = 2) + geom_point(size = 5)

```
se puede hacer la transición más suave pero no es de manera sencilla y se puede hacer manualmente. No funciona para variables discretas y no es común usarlo

```R
#Con el data frame anterior:
#Establecemos un grid de longitud 50 en el intérvalo de 1 a 3
xgrid <- with(df, seq(min(x), max(x), length = 50)) 
interp <- data.frame(
    x = xgrid,
    y = approx(df$x, df$y, xout = xgrid)$y,
    colour = approx(df$x, df$colour, xout = xgrid)$y
)
interp

ggplot(interp, aes(x, y, colour = colour)) +
    geom_line(size = 2) + geom_point(data = df, size = 5)
```

En caso de otros geom colectivos como el geom_polygon, se aplica un valor default cuando son variables continuas, en el caso de ser discretas, se dividen por default

```R
ggplot(mpg, aes(class)) + geom_bar()
ggplot(mpg, aes(class, fill = drv)) + geom_bar()
```
Si creamos un boxplot de hwy por cada cyl, sin convertir cyl en un factor, ¿Qué aes necesitamos establecer?

```R
ggplot(mpg, aes(cyl, hwy)) + geom_boxplot()

```
Respuesta: 

```R
ggplot(mpg, aes(cyl, hwy, group=cyl)) + geom_boxplot()
```

¿Que habría que modificar en la siguiente gráfica?

```R
ggplot(mpg, aes(displ, cty)) + geom_boxplot()
```

### Otros gráficos y estadísticos

#### Gráficos de superficie 
Aunque ggplot2 no cuenta con herramientas para graficar en 3D, si cuenta con algunas herramientas para representarlas en 2D, para ello tenemos los geoms de *Contour*
y *raster*, además de gráficos de burbuja usando el geom_point.

```R

View(faithfuld)
ggplot(faithfuld, aes(eruptions, waiting)) + geom_contour(aes(z = density, colour = ..level..))
```
```R
ggplot(faithfuld, aes(eruptions, waiting)) + geom_raster(aes(fill = density))
```

#### Mapas

Grafiquemos a México
```R
install.packages("maps")
data("worldMapEnv")
mi_ciudad<- map_data("world", "Mexico") %>% select(long, lat, group, id = subregion)
ggplot(mi_ciudad, aes(long, lat)) + geom_polygon(aes(group = group)) + coord_quickmap()
```

En el database de maps no se encuentra dividido por lo que no es posible el argumento group en un objeto no agrupado, pero en el caso de los estados en USA si es posible

```R
mi_counties <- map_data("county", "louisiana") %>% select(lon = long, lat, group, id = subregion)
head(mi_counties)
ggplot(mi_counties, aes(lon, lat)) + geom_polygon(aes(group = group)) + coord_quickmap()
ggplot(mi_counties, aes(lon, lat)) +
+     geom_polygon(aes(group = group), fill = NA, colour = "grey50") + coord_quickmap()
```

Formato amplio (usando solo ggplot2)
```R
ggplot(mtcars,
       aes(x=wt)) +
  geom_point(aes(y=mpg), color="red") +
  geom_point(aes(y=qsec), color="blue")
  ```

Formato long (usando el tidyverse)
```R
ea = gather(mtcars,value = "Medida",key = "Tipo",mpg,qsec)
ggplot(ea, aes(x=wt,y=Medida,color=Tipo)) + geom_point()
```

Tomando todo el potencial del tidyverse
```R
gather(mtcars,value = "Medida",key = "Tipo",mpg,qsec) %>% ggplot( aes(x=wt,y=Medida,color=Tipo)) + geom_point()
```




