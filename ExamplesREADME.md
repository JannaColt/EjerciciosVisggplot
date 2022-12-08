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




