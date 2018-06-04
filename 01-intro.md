# Introducción 



## ¿Qué es aprendizaje de máquina (machine learning)? 

Métodos **computacionales** para **aprender de datos**  con el fin
de producir reglas para 
mejorar el **desempeño** en alguna tarea o toma de decisión. 

En este curso nos enfocamos en las tareas de aprendizaje supervisado 
(predecir o estimar una variable respuesta a partir de datos de entrada) y
aprendizaje no supervisado (describir estructuras interesantes en datos,
donde no necesariamente hay una respuesta que predecir).

#### Ejemplos de tareas de aprendizaje: {-}

- Predecir si un cliente de tarjeta de crédito va a caer en impago en los próximos
tres meses.
- Reconocer palabras escritas a mano (OCR).
- Detectar llamados de ballenas en grabaciones de boyas. 
- Estimar el ingreso mensual de un hogar a partir de las características
de la vivienda, posesiones y equipamiento y localización geográfica.
- Dividir a los clientes de Netflix según sus gustos.
- Recomendar artículos a clientes de un programa de lealtad o servicio online.

Las razones usuales para intentar resolver estos problemas **computacionalmente**
son diversas:

- Quisiéramos obtener una respuesta barata, rápida, **automatizada**, y 
con suficiente precisión.
Por ejemplo, reconocer caracteres en una placa de coche de una fotografía se puede hacer por
personas, pero eso es lento y costoso. Igual oír cada segundo de grabación
de las boyas para saber si hay ballenas o no. Hacer mediciones directas
del ingreso de un hogar requiere mucho tiempo y esfuerzo.
- Quisiéramos **superar el desempeño actual** de los expertos o de reglas simples utilizando
datos: por ejemplo, en la decisión de dar o no un préstamo a un solicitante,
puede ser posible tomar mejores decisiones con algoritmos que con evaluaciones personales
o con reglas simples que toman en cuenta el ingreso mensual, por ejemplo.
- Queremos **entender de manera más completa y sistemática** el comportamiento de un fenómeno,
identificando variables o patrones importantes.

Es posible aproximarse a todos estos problemas usando reglas (por ejemplo,
si los pixeles del centro de la imagen están vacíos, entonces es un cero, 
si el crédito total es mayor al 50\% del ingreso anual, declinar el préstamo, etc)
Las razones para intentar usar **aprendizaje** para producir reglas en lugar
de intentar construir estas reglas directamente son, por ejemplo:

- Cuando conjuntos de reglas creadas a mano se desempeñan mal (por ejemplo, para
otorgar créditos, reconocer caracteres, etc.)
- Reglas creadas a mano pueden ser difíciles de mantener (por ejemplo, un corrector
ortográfico.)

#### Ejemplo: reconocimiento de dígitos escritos a mano {-}


¿Cómo reconocer los siguientes dígitos de  manera automática?



En los datos tenemos los valores de cada pixel (los caracteres son
imagenes de 16x16 pixeles), y una *etiqueta* asociada, que es el número
que la imagen representa. Podemos ver las imágenes y las etiquetas:



```r
library(tidyverse)
zip_train <- read_csv(file = 'datos/zip-train.csv')
muestra_1 <- sample_n(zip_train, 10)
graficar_digitos(muestra_1)
```

<img src="01-intro_files/figure-html/grafdigitos-1.png" width="768" />


```r
muestra_2 <- sample_n(zip_train, 10) 
graficar_digitos(muestra_2)
```

<img src="01-intro_files/figure-html/unnamed-chunk-2-1.png" width="768" />

Los 16x16=256 están escritos acomodando las filas de la imagen en 
 vector de 256 valores (cada renglón de `zip_train`). Un dígito entonces
 se representa como sigue:


```r
dim(zip_train)
```

```
## [1] 7291  257
```

```r
as.numeric(zip_train[1,])
```

```
##   [1]  6.000 -1.000 -1.000 -1.000 -1.000 -1.000 -1.000 -1.000 -0.631  0.862
##  [11] -0.167 -1.000 -1.000 -1.000 -1.000 -1.000 -1.000 -1.000 -1.000 -1.000
##  [21] -1.000 -1.000 -1.000 -0.992  0.297  1.000  0.307 -1.000 -1.000 -1.000
##  [31] -1.000 -1.000 -1.000 -1.000 -1.000 -1.000 -1.000 -1.000 -1.000 -0.410
##  [41]  1.000  0.986 -0.565 -1.000 -1.000 -1.000 -1.000 -1.000 -1.000 -1.000
##  [51] -1.000 -1.000 -1.000 -1.000 -0.683  0.825  1.000  0.562 -1.000 -1.000
##  [61] -1.000 -1.000 -1.000 -1.000 -1.000 -1.000 -1.000 -1.000 -1.000 -0.938
##  [71]  0.540  1.000  0.778 -0.715 -1.000 -1.000 -1.000 -1.000 -1.000 -1.000
##  [81] -1.000 -1.000 -1.000 -1.000 -1.000  0.100  1.000  0.922 -0.439 -1.000
##  [91] -1.000 -1.000 -1.000 -1.000 -1.000 -1.000 -1.000 -1.000 -1.000 -1.000
## [101] -0.257  0.950  1.000 -0.162 -1.000 -1.000 -1.000 -0.987 -0.714 -0.832
## [111] -1.000 -1.000 -1.000 -1.000 -1.000 -0.797  0.909  1.000  0.300 -0.961
## [121] -1.000 -1.000 -0.550  0.485  0.996  0.867  0.092 -1.000 -1.000 -1.000
## [131] -1.000  0.278  1.000  0.877 -0.824 -1.000 -0.905  0.145  0.977  1.000
## [141]  1.000  1.000  0.990 -0.745 -1.000 -1.000 -0.950  0.847  1.000  0.327
## [151] -1.000 -1.000  0.355  1.000  0.655 -0.109 -0.185  1.000  0.988 -0.723
## [161] -1.000 -1.000 -0.630  1.000  1.000  0.068 -0.925  0.113  0.960  0.308
## [171] -0.884 -1.000 -0.075  1.000  0.641 -0.995 -1.000 -1.000 -0.677  1.000
## [181]  1.000  0.753  0.341  1.000  0.707 -0.942 -1.000 -1.000  0.545  1.000
## [191]  0.027 -1.000 -1.000 -1.000 -0.903  0.792  1.000  1.000  1.000  1.000
## [201]  0.536  0.184  0.812  0.837  0.978  0.864 -0.630 -1.000 -1.000 -1.000
## [211] -1.000 -0.452  0.828  1.000  1.000  1.000  1.000  1.000  1.000  1.000
## [221]  1.000  0.135 -1.000 -1.000 -1.000 -1.000 -1.000 -1.000 -0.483  0.813
## [231]  1.000  1.000  1.000  1.000  1.000  1.000  0.219 -0.943 -1.000 -1.000
## [241] -1.000 -1.000 -1.000 -1.000 -1.000 -0.974 -0.429  0.304  0.823  1.000
## [251]  0.482 -0.474 -0.991 -1.000 -1.000 -1.000 -1.000
```

- Un enfoque más utilizado anteriormente para resolver este tipo de problemas
consistía en procesar estas imágenes con filtros hechos a mano (por ejemplo,
calcular cuántos pixeles están prendidos, si existen ciertas curvas o trazos)
para después construir reglas para determinar cada dígito. Actualmente,
el enfoque más exitoso es utilizar métodos de aprendizaje que aprendan
automáticamente esos filtros y esas reglas basadas en filtros (redes convolucionales).

#### Ejemplo:  predecir ingreso trimestral {-}

Consideramos la medición de ingreso total trimestral para una
muestra de hogares de la encuesta de ENIGH. Cada una de estas mediciones
es muy costosa en tiempo y dinero. 


```r
dat_ingreso <- read_csv(file = 'datos/enigh-ejemplo.csv')
head(dat_ingreso) %>% DT::datatable()
```

<!--html_preserve--><div id="htmlwidget-7670e812450eb9dc5115" style="width:100%;height:auto;" class="datatables html-widget"></div>
<script type="application/json" data-for="htmlwidget-7670e812450eb9dc5115">{"x":{"filter":"none","data":[["1","2","3","4","5","6"],["145074","152145","214043","096266","081090","029004"],[0,0,0,0,0,0],[1145,204,1812,1596,445,1866],[2,2,3,1,2,1],["14121","15009","21115","09012","08050","02001"],[5,4,2,5,5,5],[4,4,2,4,2,4],[2,2,2,2,1,2],[1,1,2,1,1,1],[43,53,44,45,45,74],[6,10,1,6,8,6],[4,3,2,2,1,2],[2,1,1,1,1,1],[2,2,1,1,0,1],[4,3,2,2,1,2],[2,1,1,1,1,1],[2,2,1,1,0,1],[4,3,2,2,1,2],[0,0,0,0,0,0],[4,3,2,2,1,1],[0,0,0,0,0,1],[1,2,1,1,1,0],[1,3,1,1,1,2],[1,2,1,1,1,0],[30238.13,66188.41,6170.21,14639.79,40638.35,21172.35],[30238.13,61147.41,6170.21,14639.79,40638.35,21172.35],[24065.28,56610.43,4355.42,10127.47,37646.57,12098.4],[0,26112.38,4355.42,10127.47,37646.57,0],[0,24196.8,4355.42,7520.4,29917.8,0],[0,0,0,2607.07,5484.93,0],[0,0,0,0,0,0],[0,1915.58,0,0,2243.84,0],[0,504.1,0,0,0,0],[0,504.1,0,0,0,0],[0,0,0,0,0,0],[0,0,0,0,0,0],[0,504.1,0,0,0,0],[0,0,0,0,0,0],[0,0,0,0,0,0],[0,0,0,0,0,0],[0,0,0,0,0,0],[0,0,0,0,0,0],[0,2772.55,0,0,0,0],[24065.28,0,0,0,0,0],[24065.28,0,0,0,0,0],[0,0,0,0,0,0],[0,27221.4,0,0,0,12098.4],[0,27221.4,0,0,0,12098.4],[0,0,0,0,0,0],[0,0,0,0,0,0],[0,0,0,0,0,0],[0,0,0,0,0,0],[0,0,0,0,0,0],[6172.85,4536.98,1814.79,4512.32,2991.78,9073.95],[0,0,0,0,0,0],[0,0,0,0,0,0],[156.43,0,0,0,0,0],[156.43,0,0,0,0,0],[0,0,0,0,0,0],[6016.42,4536.98,1814.79,4512.32,2991.78,9073.95],[0,5041,0,0,0,0],[0,5041,0,0,0,0],[0,5041,0,0,0,0],[0,0,0,0,0,0],[0,0,0,0,0,0],[0,0,0,0,0,0],[32029.35,57438.81,5721.15,14895.44,21550.06,16276.64],[32029.35,48667.47,5721.15,14895.44,21550.06,16276.64],[25856.5,44130.49,3906.36,10383.12,18558.28,7202.69],[10447.98,20970.87,2477.22,6981.87,2917.01,3335.68],[7580.17,16186.88,2477.22,6981.87,1944.68,3335.68],[827.76,3951.7,629.14,1147.14,453.77,904.37],[2033.55,4810.2,262.14,2059.63,453.75,760.2],[0,0,0,321.98,155.57,0],[2776.56,1127.18,301.46,1088.46,311.14,583.25],[104.28,235.92,262.14,130.36,207.44,196.6],[0,393.21,262.14,259.41,0,0],[0,131.07,0,182.5,0,0],[0,2188.83,760.2,958.11,77.79,366.99],[0,825.72,0,495.36,0,366.99],[0,235.92,0,0,0,157.28],[0,327.67,0,0,0,0],[0,249.03,0,52.14,0,0],[1668.56,1245.14,0,0,0,0],[169.46,465.29,0,286.78,285.22,0],[2867.81,4783.99,0,0,972.33,0],[0,0,0,0,0,0],[198.54,1290.5,0,0,1527.8,0],[0,1290.5,0,0,0,0],[198.54,0,0,0,1527.8,0],[3471.96,1632.3,400.76,451.23,1721.52,98.3],[0,0,0,0,0,0],[263.22,57.97,181.48,0,91,27.73],[200.55,0,105.86,0,493.64,0],[3008.19,1574.33,113.42,451.23,1136.88,70.57],[1022.79,1328.84,384.13,463.27,2964.86,196.6],[1022.79,986.05,384.13,463.27,2964.86,196.6],[0,100.82,0,0,0,0],[0,241.97,0,0,0,0],[0,0,0,50.14,0,0],[0,0,0,50.14,0,0],[0,0,0,0,0,0],[0,0,0,0,0,0],[7186.59,8680.72,393.21,1814.91,1163.8,2782.68],[1564.26,2752.43,393.21,912.45,0,0],[481.31,302.46,0,0,0,0],[0,0,0,0,0,0],[3128.54,1935.77,0,0,0,2782.68],[0,604.92,0,0,0,0],[3128.54,1330.85,0,0,0,2782.68],[2012.48,3690.06,0,902.46,1163.8,0],[1438.92,2601.2,0,0,4637.26,0],[1052.87,0,0,0,0,0],[386.05,2601.2,0,0,4637.26,0],[0,0,0,0,0,0],[2089.72,4455.27,251.04,621.7,3626.03,789.43],[1588.35,1355.05,251.04,421.15,3626.03,789.43],[0,100.82,0,200.55,0,0],[501.37,2999.4,0,0,0,0],[0,3170.79,0,0,0,0],[0,8771.34,0,0,0,0],[0,8771.34,0,0,0,0],[0,0,0,0,0,0],[0,8771.34,0,0,0,0],[0,7259.04,0,0,0,0],[0,1512.3,0,0,0,0],[0,0,0,0,0,0],[0,0,0,0,0,0],[0,0,0,0,0,0],[0,0,0,0,0,0],[0,0,0,0,0,0],[0,0,0,0,0,0],[4902.3,4902.3,4902.3,5171.4,4902.3,5171.4],["1422","1522","2131","0912","0823","0214"],["18200","22980","27130","10680","07100","00950"],[1,1,1,1,1,1],[8,7,8,8,8,8],[9,9,3,9,9,6],[3,2,1,2,3,2],[12,44,30,7,0,40],[null,null,null,null,1,null],[1,1,2,1,1,1],[2,2,null,2,2,2],[3,3,1,2,1,2],[4,6,1,5,6,3],[1,1,1,1,1,1],[2,1,1,1,1,1],[1,1,1,1,1,1],[2,2,1,2,2,2],[1,1,2,2,1,1],[1,1,1,1,1,1],[1,1,1,1,1,1],[11,10,1,5,8,4],[3,3,1,3,3,3],[null,null,2,null,null,null],[1,1,4,1,1,1],[4,4,2,4,2,4],["01","02",null,"01",null,"01"],[0,1,null,1,null,0],["02",null,null,null,null,null],[0,null,null,null,null,null],[3,1,null,1,null,1],[5,1,null,5,null,5],[0,0,0,0,0,0],[0,0,0,0,0,0],[2000,1500,600,1500,1000,3000],[6016.42,4536.98,1814.79,4512.32,2991.78,9073.95],[0,0,0,0,0,0],[0,0,0,0,0,0],[null,null,null,null,null,null],[3,1,null,3,null,3],[null,1,null,null,null,null],[1,1,1,1,1,2],[1,1,2,2,1,1],[1,1,2,1,1,1],[1,1,2,1,2,2],[1,2,2,2,2,2],[1,2,1,2,2,2],[1,1,2,1,1,1],[1,1,1,2,1,1],[1,2,2,2,2,2],[1,1,2,2,2,2],[2,2,2,2,2,2],[2,2,2,2,2,2],[4,3,2,2,1,2],[1,1,1,1,1,1],[0,0,0,0,0,0],[2,2,2,2,2,2],[0,0,0,0,0,0],[0,0,0,0,0,0],[2,2,2,2,2,2],[0,0,0,0,0,0],[0,0,0,0,0,0],[2,2,1,2,2,2],[2,2,1,2,2,2],[2,2,1,2,2,2],[2,2,1,2,2,2],[2,2,2,2,2,2],[2,2,2,2,2,2],[null,null,1,null,null,null],[null,null,1,null,null,null],[null,null,2,null,null,null],[null,null,1,null,null,null],[null,null,1,null,null,null],[null,null,1,null,null,null],[null,null,1,null,null,null],[null,null,1,null,null,null],[null,null,2,null,null,null],[null,null,1,null,null,null],[1,1,2,1,1,2],[1,1,2,1,1,2],[1,1,2,2,1,2],[1,1,2,2,2,2],[1,3,0,0,1,1],["08","07",null,null,"07","02"],[0,0,0,0,0,0],[null,null,null,null,null,null],[1,0,0,0,0,0],["08",null,null,null,null,null],[0,2,0,0,0,0],[null,"05",null,null,null,null],[0,1,0,0,0,0],[null,"70",null,null,null,null],[0,0,0,0,0,0],[null,null,null,null,null,null],[0,0,0,0,0,0],[null,null,null,null,null,null],[0,0,0,0,0,0],[null,null,null,null,null,null],[0,0,0,0,0,0],[null,null,null,null,null,null],[1,1,0,1,1,1],["02","05",null,"05","02","90"],[0,0,0,0,0,0],[null,null,null,null,null,null],[0,0,0,0,0,0],[null,null,null,null,null,null],[3,2,1,2,1,2],["06","04","08","05","08","04"],[1,1,0,1,1,1],["09","03",null,"05","07","04"],[0,0,0,0,0,0],[null,null,null,null,null,null],[1,1,1,1,1,1],["01","95","09","05","02","95"],[1,0,0,0,0,1],["01",null,null,null,null,"92"],[1,1,0,1,1,1],["01","08",null,"06","08","00"],[1,1,0,1,1,1],["00","08",null,"05","06","00"],[1,1,1,1,1,1],["00","05","07","05","00","00"],[1,2,0,1,1,1],["00","07",null,"05","07","04"],[1,1,1,1,1,1],["07","08","05","05","06","00"],[0,1,0,0,0,1],[null,"05",null,null,null,"90"],[3,0,0,0,0,1],["02",null,null,null,null,"09"],[0,0,0,0,0,0],[null,null,null,null,null,null],[1,3,0,0,0,0],["07","09",null,null,null,null],[1,1,0,0,0,0],["07","09",null,null,null,null],[0,0,0,0,0,0],[null,null,null,null,null,null],[0,4,0,0,0,0],[0,0,30,40,15,5],[1,0,0,0,0,0],[0,0,0,0,0,0],[24,0,0,0,0,0],[null,null,null,null,1,null],[null,2,2,2,null,null],[null,null,null,null,null,null],[null,null,null,null,null,null],[null,5,null,null,5,5],[6,null,null,null,null,null],[2,1,2,2,2,null],[null,null,null,null,null,null],[1,1,1,1,1,1],[2,2,2,2,2,2],[2,2,2,2,2,2],[2,2,2,2,2,2],[2,2,2,2,2,2],[2,2,2,2,2,2],[2,2,2,2,2,2],[2,2,2,2,2,2],[2,2,2,2,2,2],[2,2,2,2,2,2],[2,2,2,2,2,2],[3800,6500,700,1500,1600,2000],[500,500,150,280,0,0],[1145,204,1812,1596,445,1866],[2,2,3,1,2,1],["14121","15009","21115","09012","08050","02001"],[5,4,2,5,5,5],["1422","1522","2131","0912","0823","0214"],["18200","22980","27130","10680","07100","00950"],[14,15,21,9,8,2],[121,9,115,12,50,1],["De 15 mil a 100 mil","De 15 mil a 100 mil","De 2500 a 15 mil","100 mil o más","De 15 mil a 100 mil","100 mil o más"],["Muy bajo","Bajo","Alto","Muy bajo","Muy bajo","Muy bajo"],[14,15,21,9,8,2],["Jalisco","México","Puebla","Distrito Federal","Chihuahua","Baja California"],[0.593588186308115,0.880075765799194,0.0410124985846622,0.245020969011429,0.725832256438428,0.409424505436617],["[10.28,10.51)","[10.76,11.14)","[ 0.00, 9.05)","[ 9.40, 9.66)","[10.51,10.76)","[ 9.88,10.08)"],[10.3168920600383,11.10027575934,8.72765020788304,9.59156674780805,10.61249208803,9.96049859440625],[7.60761984728961,8.21366895865512,0,6.80623183677775,7.06030467744242,0],[-2.70927221274873,-2.88660680068493,-8.72765020788304,-2.78533491103031,-3.55218741058755,-9.96049859440625],[5,4,2,5,5,5],[2,2,3,1,2,1],["Jalisco","México","Puebla","Distrito Federal","Chihuahua","Baja California"],["Zapotiltic","Amecameca","Quecholac","Tlalpan","Nuevo Casas Grandes","Ensenada"],[30957529,44306251,50643674,139753252,101061021,1980549333],[5807667,8581318,9651415,26960561,28476664,530609223],[36708,113458,113041,415378,137491,454765],[29192,48421,47281,650567,59337,466814],[14149,23175,22988,312014,28929,232283],[14979,25145,24181,338334,30170,229609],[1537,2637,3425,26642,3284,25153],[791,1351,1736,13691,1681,12763],[746,1286,1689,12951,1603,12390],[27519,45329,42975,606707,55655,433910],[13322,21647,20866,289827,27170,218101],[14197,23682,22109,316880,28485,215809],[26457,43426,40574,586980,53130,416105],[12792,20674,19661,279830,25940,209029],[13665,22752,20913,307150,27190,207076],[22612,36606,32201,516479,44724,353615],[10895,17285,15337,243918,21758,177417],[11717,19321,16864,272561,22966,176198],[20945,33882,28886,486355,41299,326426],[10037,15899,13649,228566,20005,163622],[10908,17983,15237,257789,21294,162804],[19174,31022,25629,454056,37749,298661],[9126,14474,12032,212256,18223,149430],[10048,16548,13597,241800,19526,149231],[1561,2872,3561,29543,3782,26543],[778,1446,1833,15002,1841,13558],[783,1426,1728,14541,1941,12985],[3346,5851,7213,60685,7149,53752],[1649,2916,3696,30907,3571,27126],[1697,2935,3517,29778,3578,26626],[3872,6566,8097,70711,8229,63814],[1941,3318,4159,36147,4137,32367],[1931,3248,3938,34564,4092,31447],[1667,2724,3315,30124,3425,27189],[858,1386,1688,15352,1753,13795],[809,1338,1627,14772,1672,13394],[1771,2860,3257,32299,3550,27765],[911,1425,1617,16310,1782,14192],[860,1435,1640,15989,1768,13573],[3705,6079,6330,79861,7559,62966],[1805,2954,2947,39567,3746,32144],[1900,3125,3383,40294,3813,30822],[7780,13649,12234,184749,15961,126271],[3555,4211,3167,66090,5400,35069],[1742,1914,1502,28880,2580,16975],[1813,2297,1665,37210,2820,18094],[1589.98,3072.29,4106.7,11616.88,2727.76,62179.06],[51.86,74,116.8,211.43,77.56,1203.57],[27461,42799,44773,496924,53946,265377],[13307,20586,21933,242947,26263,133893],[14154,22213,22840,253977,27683,131484],[1185,5028,1436,129909,3628,183076],[610,2343,582,57521,1896,91400],[575,2685,854,72388,1732,91676],[25444,42387,39591,563173,50867,378746],[12166,20159,19105,268389,24703,188693],[13278,22228,20486,294784,26164,190053],[329,688,458,17242,693,28466],[172,302,203,8096,369,15010],[157,386,255,9146,324,13456],[39,155,80,10334,289,23531],[21,75,43,4723,156,11924],[18,80,37,5611,133,11607],[0,1,0,54,24,1350],[0,0,0,11,7,449],[0,1,0,43,17,901],[12,75,36,8705,127,20609],[7,33,19,3990,72,10711],[5,42,17,4715,55,9898],[36,153,78,10283,277,23040],[0,1,0,51,19,1203],[11,75,36,8674,122,20308],[82,406,216,23368,535,45938],[1721,1750,1485,25847,2964,16282],[1089,1006,781,14731,1711,9375],[449,393,384,6792,911,3963],[133,154,199,1988,226,1363],[195,191,149,2890,313,1352],[120,79,90,1603,152,874],[128,81,64,1442,96,707],[156,142,99,2921,222,1597],[27299,45962,44615,602361,55714,437337],[700,1255,2374,10041,2243,13729],[371,642,1201,5082,1073,7103],[329,613,1173,4959,1170,6626],[76,157,473,1256,185,2095],[41,84,261,638,100,1090],[35,73,212,618,85,1005],[145,149,882,1353,288,2101],[68,77,486,784,161,1084],[77,72,396,569,127,1017],[1133,2009,1179,24900,2518,19106],[560,1006,538,12459,1246,9615],[573,1003,641,12441,1272,9491],[897,1708,587,35215,2496,19015],[460,832,265,17955,1230,9679],[437,876,322,17260,1266,9336],[70,107,609,889,122,2280],[43,63,367,514,72,1244],[27,44,242,375,50,1036],[1239,1220,5500,10839,879,15330],[565,434,2258,3351,455,6530],[674,786,3242,7488,424,8800],[1397,1351,5261,17375,1152,19181],[679,500,2196,6386,588,8758],[718,851,3065,10989,564,10423],[3546,3125,7363,28611,5027,39421],[1616,1274,3562,11539,2586,20423],[1930,1851,3801,17072,2441,18998],[4144,5769,7578,54858,8510,44620],[1879,2613,3726,24260,3967,21960],[2265,3156,3852,30598,4543,22660],[1163,1284,1401,21986,2166,24578],[649,714,727,12248,1340,13865],[514,570,674,9738,826,10713],[4773,9880,4400,94108,8927,66709],[2390,4968,2105,45999,4157,33695],[2383,4912,2295,48109,4770,33014],[5111,11152,2386,251452,12926,118211],[2477,5244,1120,120802,6304,58762],[2634,5908,1266,130650,6622,59449],[94.93,236.15,167.99,821.86,167.24,3720.47],[93.69,236.91,166.87,819.68,168.69,3683.18],[96.4,234.74,166.93,824.76,166.17,3740.62],[10558,18346,16004,294585,23245,204364],[7442,12652,12262,174263,15561,128103],[3116,5694,3742,120322,7684,76261],[11923,18102,16028,219031,21187,144845],[3374,4530,2986,68204,6026,45716],[8549,13572,13042,150827,15161,99129],[9700,17401,15304,280680,21772,196553],[6777,11877,11627,164759,14526,122253],[2923,5524,3677,115921,7246,74300],[858,945,700,13905,1473,7811],[665,775,635,9504,1035,5850],[193,170,65,4401,438,1961],[9923,24744,29172,263632,15802,108225],[19111,23103,17124,367523,42842,346854],[12291,8527,2070,189196,27170,172870],[640,2163,210,87660,2648,25046],[52,1410,48,628,241,16742],[6108,7423,14596,39363,9015,119621],[8148,12370,10991,190787,14400,123523],[12369,20652,19051,267792,25063,190213],[2074,3520,2103,56412,5031,39131],[27489,43145,44542,531806,47237,303417],[1254,3195,1229,44163,8320,86546],[1,108,5,1790,1,616],[253,556,91,40734,2151,61900],[7481,11227,10043,170369,16876,127204],[5713,8609,8172,121148,12690,94109],[1768,2618,1871,49221,4186,33095],[29039,47941,46407,629837,58924,454073],[23452,38259,39040,473593,46722,348443],[5587,9682,7367,156244,12202,105630],[7527,11366,10319,176086,16975,129657],[9200,12897,11332,188583,20795,164562],[7481,11227,10043,170369,16876,127204],[1415,1087,812,12462,3058,25230],[304,583,477,5752,861,12128],[29039,47941,46407,629837,58924,454073],[54.16,130.28,173.39,385.57,116.55,1667.49],[14.96,44.31,70.71,139.83,36.55,629.74],[7059,10187,8915,166633,16684,120034],[413,1001,1097,2337,121,4433],[2066,4461,4820,52780,4945,43151],[5410,6741,5188,116826,11861,81618],[152,1156,1224,12796,449,12022],[984,2654,2916,22254,1693,18874],[6323,7368,5856,134239,14644,93689],[7420,11105,9812,169388,16776,121783],[57,87,194,419,53,3043],[7032,10789,8669,147569,16662,112712],[440,390,1342,21609,142,11776],[7395,11008,9457,168403,16604,123164],[7394,10384,8962,167792,16366,104654],[79,792,1030,1282,382,19586],[6942,10065,7806,146573,16238,98904],[64,138,593,527,30,1959],[6341,9559,5464,157860,14953,99651],[7221,10763,9018,166598,16297,117853],[6757,7724,3947,151917,16320,108645],[6024,6239,3471,134366,14888,96389],[3875,3349,2428,93095,12587,84966],[1821,2392,460,88544,5664,50430],[3550,3758,1362,123912,7524,56639],[4779,7050,3260,134358,12916,102392],[1119,1505,258,73181,3379,39688],[50,75,92,182,109,1915],[25431,39115,38339,641550,55553,398122],["[10.28,10.76)","[10.76,14.35]","[ 0.00, 9.40)","[ 9.40, 9.88)","[10.28,10.76)","[ 9.88,10.28)"],[3,7,0,6,4,3],["01","01","01","01","01","01"],[8301,null,9111,5242,8341,null],[83,null,91,52,83,null]],"container":"<table class=\"display\">\n  <thead>\n    <tr>\n      <th> <\/th>\n      <th>FOLIOVIV<\/th>\n      <th>FOLIOHOG<\/th>\n      <th>FACTOR<\/th>\n      <th>TAM_LOC<\/th>\n      <th>UBICA_GEO<\/th>\n      <th>CONAPO<\/th>\n      <th>TENENCIA<\/th>\n      <th>CLASE_HOG<\/th>\n      <th>SEXO<\/th>\n      <th>EDAD<\/th>\n      <th>ED_FORMAL<\/th>\n      <th>TAM_HOG<\/th>\n      <th>HOMBRES<\/th>\n      <th>MUJERES<\/th>\n      <th>TOT_RESI<\/th>\n      <th>TOT_HOM<\/th>\n      <th>TOT_MUJ<\/th>\n      <th>MAYORES<\/th>\n      <th>MENORES<\/th>\n      <th>P12_64<\/th>\n      <th>P65MAS<\/th>\n      <th>N_OCUP<\/th>\n      <th>PERING<\/th>\n      <th>PEROCU<\/th>\n      <th>INGTOT<\/th>\n      <th>INGCOR<\/th>\n      <th>INGMON<\/th>\n      <th>TRABAJO<\/th>\n      <th>SUELDOS<\/th>\n      <th>HOREXT<\/th>\n      <th>COMIS<\/th>\n      <th>OTRA_REM<\/th>\n      <th>NEGOCIO<\/th>\n      <th>NOAGROP<\/th>\n      <th>INDUSTRIA<\/th>\n      <th>COMERCIO<\/th>\n      <th>SERVICIOS<\/th>\n      <th>AGROPE<\/th>\n      <th>AGRICOLAS<\/th>\n      <th>PECUARIOS<\/th>\n      <th>REPRODUCC<\/th>\n      <th>PESCA<\/th>\n      <th>OTROS_TRAB<\/th>\n      <th>RENTAS<\/th>\n      <th>UTILIDAD<\/th>\n      <th>ARRENDA<\/th>\n      <th>TRANSFER<\/th>\n      <th>JUBILA<\/th>\n      <th>BECA<\/th>\n      <th>DONATIVO<\/th>\n      <th>REMESA<\/th>\n      <th>BENE_GOB<\/th>\n      <th>OTROS<\/th>\n      <th>GASNOM<\/th>\n      <th>AUTOC<\/th>\n      <th>REM_ESP<\/th>\n      <th>REGA<\/th>\n      <th>ESP_HOG<\/th>\n      <th>ESP_INST<\/th>\n      <th>ESTI<\/th>\n      <th>PERTOT<\/th>\n      <th>PERMON<\/th>\n      <th>RETIRO<\/th>\n      <th>PRESTAMO<\/th>\n      <th>OTRAS_PR<\/th>\n      <th>ERONOM<\/th>\n      <th>GASTOT<\/th>\n      <th>GASCOR<\/th>\n      <th>GASMON<\/th>\n      <th>ALIMENTOS<\/th>\n      <th>ALI_DENT<\/th>\n      <th>CEREALES<\/th>\n      <th>CARNES<\/th>\n      <th>PESCADO<\/th>\n      <th>LECHE<\/th>\n      <th>HUEVO<\/th>\n      <th>ACEITES<\/th>\n      <th>TUBERCULO<\/th>\n      <th>VERDURAS<\/th>\n      <th>FRUTAS<\/th>\n      <th>AZUCAR<\/th>\n      <th>CAFE<\/th>\n      <th>ESPECIAS<\/th>\n      <th>OTRO_ALI<\/th>\n      <th>BEBIDAS<\/th>\n      <th>FUERA_HOG<\/th>\n      <th>TABACO<\/th>\n      <th>VESTIDO_C<\/th>\n      <th>VESTIDO<\/th>\n      <th>CALZADO<\/th>\n      <th>VIVIENDA<\/th>\n      <th>ALQUILER<\/th>\n      <th>PRED_CONS<\/th>\n      <th>AGUA<\/th>\n      <th>ENERGIA<\/th>\n      <th>LIMPIEZA<\/th>\n      <th>CUIDADOS<\/th>\n      <th>UTENSILIOS<\/th>\n      <th>ENSERES<\/th>\n      <th>SALUD<\/th>\n      <th>ATEN_PRI<\/th>\n      <th>HOSPITAL<\/th>\n      <th>MEDICA<\/th>\n      <th>TRANSPORTE<\/th>\n      <th>PUBLICO<\/th>\n      <th>FORANEO<\/th>\n      <th>ADQUI<\/th>\n      <th>MANTE<\/th>\n      <th>REFA<\/th>\n      <th>COMBUS<\/th>\n      <th>COMUNICA<\/th>\n      <th>EDUCACION<\/th>\n      <th>EDUCA<\/th>\n      <th>ESPARCI<\/th>\n      <th>PAQUETE<\/th>\n      <th>PERSONAL<\/th>\n      <th>CUIDADO<\/th>\n      <th>ACCESORIO<\/th>\n      <th>OTROS_GAS<\/th>\n      <th>TRANSFE<\/th>\n      <th>EROTOT<\/th>\n      <th>EROMON<\/th>\n      <th>CUOTA<\/th>\n      <th>MAT_SERV<\/th>\n      <th>MATERIAL<\/th>\n      <th>SERVICIO<\/th>\n      <th>DEPOSITO<\/th>\n      <th>TERCEROS<\/th>\n      <th>PAGO_TAR<\/th>\n      <th>DEUDAS<\/th>\n      <th>BALANCE<\/th>\n      <th>OTRA_ERO<\/th>\n      <th>SMG<\/th>\n      <th>EST_DIS<\/th>\n      <th>UPM<\/th>\n      <th>CLAVIV<\/th>\n      <th>PARED<\/th>\n      <th>TECHOS<\/th>\n      <th>PISOS<\/th>\n      <th>ANTIGUA<\/th>\n      <th>ANTIGUA_NE<\/th>\n      <th>CUA_COC<\/th>\n      <th>COC_DUER<\/th>\n      <th>DORMI<\/th>\n      <th>CUART<\/th>\n      <th>DIS_AGUA<\/th>\n      <th>DOT_AGUA<\/th>\n      <th>EXCUS<\/th>\n      <th>USO_COM<\/th>\n      <th>ADM_AG<\/th>\n      <th>DRENAJE<\/th>\n      <th>ELECT<\/th>\n      <th>FOCOS<\/th>\n      <th>COMBUS_1<\/th>\n      <th>ESTUFA<\/th>\n      <th>ELI_BA<\/th>\n      <th>TENEN<\/th>\n      <th>TENEN_NR1<\/th>\n      <th>TENEN_NH1<\/th>\n      <th>TENEN_NR2<\/th>\n      <th>TENEN_NH2<\/th>\n      <th>ESCRI<\/th>\n      <th>FINAN<\/th>\n      <th>RENTA<\/th>\n      <th>RENTA_TRI<\/th>\n      <th>ESTIM<\/th>\n      <th>ESTIM_TRI<\/th>\n      <th>PAGOVIV<\/th>\n      <th>PAGOVIV_TR<\/th>\n      <th>PAGO_MESP<\/th>\n      <th>ADQUI_1<\/th>\n      <th>VIV_USADA<\/th>\n      <th>LAVAD<\/th>\n      <th>FREGAD<\/th>\n      <th>REGAD<\/th>\n      <th>TIN_AZO<\/th>\n      <th>CISTE<\/th>\n      <th>PILETA<\/th>\n      <th>CALEN<\/th>\n      <th>MED_LUZ<\/th>\n      <th>BOM_AG<\/th>\n      <th>TAN_GAS<\/th>\n      <th>AIRE_ACO<\/th>\n      <th>CALEF<\/th>\n      <th>RESIDENTES<\/th>\n      <th>HOGARES2<\/th>\n      <th>HOGARES3<\/th>\n      <th>HUESPEDES4<\/th>\n      <th>HUESPEDES5<\/th>\n      <th>HUESPEDES6<\/th>\n      <th>DOMESTICO7<\/th>\n      <th>DOMESTICO8<\/th>\n      <th>DOMESTICO9<\/th>\n      <th>ALIM1<\/th>\n      <th>ALIM2<\/th>\n      <th>ALIM3<\/th>\n      <th>ALIM4<\/th>\n      <th>ALIM5<\/th>\n      <th>ALIM6<\/th>\n      <th>ALIM7<\/th>\n      <th>ALIM8<\/th>\n      <th>ALIM9<\/th>\n      <th>ALIM10<\/th>\n      <th>ALIM11<\/th>\n      <th>ALIM12<\/th>\n      <th>ALIM13<\/th>\n      <th>ALIM14<\/th>\n      <th>ALIM15<\/th>\n      <th>ALIM16<\/th>\n      <th>SERV_1<\/th>\n      <th>SERV_2<\/th>\n      <th>SERV_3<\/th>\n      <th>SERV_4<\/th>\n      <th>VEHI1_N<\/th>\n      <th>VEHI1_A<\/th>\n      <th>VEHI2_N<\/th>\n      <th>VEHI2_A<\/th>\n      <th>VEHI3_N<\/th>\n      <th>VEHI3_A<\/th>\n      <th>VEHI4_N<\/th>\n      <th>VEHI4_A<\/th>\n      <th>VEHI5_N<\/th>\n      <th>VEHI5_A<\/th>\n      <th>VEHI6_N<\/th>\n      <th>VEHI6_A<\/th>\n      <th>VEHI7_N<\/th>\n      <th>VEHI7_A<\/th>\n      <th>VEHI8_N<\/th>\n      <th>VEHI8_A<\/th>\n      <th>VEHI9_N<\/th>\n      <th>VEHI9_A<\/th>\n      <th>EQH1_N<\/th>\n      <th>EQH1_A<\/th>\n      <th>EQH2_N<\/th>\n      <th>EQH2_A<\/th>\n      <th>EQH3_N<\/th>\n      <th>EQH3_A<\/th>\n      <th>EQH4_N<\/th>\n      <th>EQH4_A<\/th>\n      <th>EQH5_N<\/th>\n      <th>EQH5_A<\/th>\n      <th>EQH6_N<\/th>\n      <th>EQH6_A<\/th>\n      <th>EQH7_N<\/th>\n      <th>EQH7_A<\/th>\n      <th>EQH8_N<\/th>\n      <th>EQH8_A<\/th>\n      <th>EQH9_N<\/th>\n      <th>EQH9_A<\/th>\n      <th>EQH10_N<\/th>\n      <th>EQH10_A<\/th>\n      <th>EQH11_N<\/th>\n      <th>EQH11_A<\/th>\n      <th>EQH12_N<\/th>\n      <th>EQH12_A<\/th>\n      <th>EQH13_N<\/th>\n      <th>EQH13_A<\/th>\n      <th>EQH14_N<\/th>\n      <th>EQH14_A<\/th>\n      <th>EQH15_N<\/th>\n      <th>EQH15_A<\/th>\n      <th>EQH16_N<\/th>\n      <th>EQH16_A<\/th>\n      <th>EQH17_N<\/th>\n      <th>EQH17_A<\/th>\n      <th>EQH18_N<\/th>\n      <th>EQH18_A<\/th>\n      <th>EQH19_N<\/th>\n      <th>EQH19_A<\/th>\n      <th>TSALUD1_H<\/th>\n      <th>TSALUD1_M<\/th>\n      <th>TSALUD1_C<\/th>\n      <th>TSALUD2_H<\/th>\n      <th>TSALUD2_M<\/th>\n      <th>HABITO_1<\/th>\n      <th>HABITO_2<\/th>\n      <th>HABITO_3<\/th>\n      <th>HABITO_4<\/th>\n      <th>HABITO_5<\/th>\n      <th>HABITO_6<\/th>\n      <th>CONSUMO<\/th>\n      <th>NR_VIV<\/th>\n      <th>TARJETA<\/th>\n      <th>PAGOTARJET<\/th>\n      <th>REGALOTAR<\/th>\n      <th>REGALODADO<\/th>\n      <th>AUTOCONS<\/th>\n      <th>REGALOS<\/th>\n      <th>REMUNERA<\/th>\n      <th>TRANSFEREN<\/th>\n      <th>PARTO_G<\/th>\n      <th>EMBARAZO_G<\/th>\n      <th>NEGCUA<\/th>\n      <th>EST_ALIM<\/th>\n      <th>EST_TRANS<\/th>\n      <th>FACTOR_1<\/th>\n      <th>TAM_LOC_1<\/th>\n      <th>UBICA_GEO_1<\/th>\n      <th>CONAPO_1<\/th>\n      <th>EST_DIS_1<\/th>\n      <th>UPM_1<\/th>\n      <th>ENT<\/th>\n      <th>MUN<\/th>\n      <th>tamaño_localidad<\/th>\n      <th>marginación<\/th>\n      <th>ENTIDAD<\/th>\n      <th>NOM_ENT<\/th>\n      <th>percentil<\/th>\n      <th>decil<\/th>\n      <th>log.ingreso<\/th>\n      <th>log.comunica<\/th>\n      <th>log.prop<\/th>\n      <th>conapo.f<\/th>\n      <th>tam_loc.f<\/th>\n      <th>NOM_ENT_1<\/th>\n      <th>NOM_MUN<\/th>\n      <th>LONGITUD<\/th>\n      <th>LATITUD<\/th>\n      <th>ALTITUD<\/th>\n      <th>POBTOT<\/th>\n      <th>POBMAS<\/th>\n      <th>POBFEM<\/th>\n      <th>P_0A2<\/th>\n      <th>P_0A2_M<\/th>\n      <th>P_0A2_F<\/th>\n      <th>P_3YMAS<\/th>\n      <th>P_3YMAS_M<\/th>\n      <th>P_3YMAS_F<\/th>\n      <th>P_5YMAS<\/th>\n      <th>P_5YMAS_M<\/th>\n      <th>P_5YMAS_F<\/th>\n      <th>P_12YMAS<\/th>\n      <th>P_12YMAS_M<\/th>\n      <th>P_12YMAS_F<\/th>\n      <th>P_15YMAS<\/th>\n      <th>P_15YMAS_M<\/th>\n      <th>P_15YMAS_F<\/th>\n      <th>P_18YMAS<\/th>\n      <th>P_18YMAS_M<\/th>\n      <th>P_18YMAS_F<\/th>\n      <th>P_3A5<\/th>\n      <th>P_3A5_M<\/th>\n      <th>P_3A5_F<\/th>\n      <th>P_6A11<\/th>\n      <th>P_6A11_M<\/th>\n      <th>P_6A11_F<\/th>\n      <th>P_8A14<\/th>\n      <th>P_8A14_M<\/th>\n      <th>P_8A14_F<\/th>\n      <th>P_12A14<\/th>\n      <th>P_12A14_M<\/th>\n      <th>P_12A14_F<\/th>\n      <th>P_15A17<\/th>\n      <th>P_15A17_M<\/th>\n      <th>P_15A17_F<\/th>\n      <th>P_18A24<\/th>\n      <th>P_18A24_M<\/th>\n      <th>P_18A24_F<\/th>\n      <th>P_15A49_F<\/th>\n      <th>P_60YMAS<\/th>\n      <th>P_60YMAS_M<\/th>\n      <th>P_60YMAS_F<\/th>\n      <th>REL_H_M<\/th>\n      <th>PROM_HNV<\/th>\n      <th>PNACENT<\/th>\n      <th>PNACENT_M<\/th>\n      <th>PNACENT_F<\/th>\n      <th>PNACOE<\/th>\n      <th>PNACOE_M<\/th>\n      <th>PNACOE_F<\/th>\n      <th>PRES2005<\/th>\n      <th>PRES2005_M<\/th>\n      <th>PRES2005_F<\/th>\n      <th>PRESOE05<\/th>\n      <th>PRESOE05_M<\/th>\n      <th>PRESOE05_F<\/th>\n      <th>P3YM_HLI<\/th>\n      <th>P3YM_HLI_M<\/th>\n      <th>P3YM_HLI_F<\/th>\n      <th>P3HLINHE<\/th>\n      <th>P3HLINHE_M<\/th>\n      <th>P3HLINHE_F<\/th>\n      <th>P3HLI_HE<\/th>\n      <th>P3HLI_HE_M<\/th>\n      <th>P3HLI_HE_F<\/th>\n      <th>P5_HLI<\/th>\n      <th>P5_HLI_NHE<\/th>\n      <th>P5_HLI_HE<\/th>\n      <th>PHOG_IND<\/th>\n      <th>PCON_LIM<\/th>\n      <th>PCLIM_MOT<\/th>\n      <th>PCLIM_VIS<\/th>\n      <th>PCLIM_LENG<\/th>\n      <th>PCLIM_AUD<\/th>\n      <th>PCLIM_MOT2<\/th>\n      <th>PCLIM_MEN<\/th>\n      <th>PCLIM_MEN2<\/th>\n      <th>PSIN_LIM<\/th>\n      <th>P3A5_NOA<\/th>\n      <th>P3A5_NOA_M<\/th>\n      <th>P3A5_NOA_F<\/th>\n      <th>P6A11_NOA<\/th>\n      <th>P6A11_NOAM<\/th>\n      <th>P6A11_NOAF<\/th>\n      <th>P12A14NOA<\/th>\n      <th>P12A14NOAM<\/th>\n      <th>P12A14NOAF<\/th>\n      <th>P15A17A<\/th>\n      <th>P15A17A_M<\/th>\n      <th>P15A17A_F<\/th>\n      <th>P18A24A<\/th>\n      <th>P18A24A_M<\/th>\n      <th>P18A24A_F<\/th>\n      <th>P8A14AN<\/th>\n      <th>P8A14AN_M<\/th>\n      <th>P8A14AN_F<\/th>\n      <th>P15YM_AN<\/th>\n      <th>P15YM_AN_M<\/th>\n      <th>P15YM_AN_F<\/th>\n      <th>P15YM_SE<\/th>\n      <th>P15YM_SE_M<\/th>\n      <th>P15YM_SE_F<\/th>\n      <th>P15PRI_IN<\/th>\n      <th>P15PRI_INM<\/th>\n      <th>P15PRI_INF<\/th>\n      <th>P15PRI_CO<\/th>\n      <th>P15PRI_COM<\/th>\n      <th>P15PRI_COF<\/th>\n      <th>P15SEC_IN<\/th>\n      <th>P15SEC_INM<\/th>\n      <th>P15SEC_INF<\/th>\n      <th>P15SEC_CO<\/th>\n      <th>P15SEC_COM<\/th>\n      <th>P15SEC_COF<\/th>\n      <th>P18YM_PB<\/th>\n      <th>P18YM_PB_M<\/th>\n      <th>P18YM_PB_F<\/th>\n      <th>GRAPROES<\/th>\n      <th>GRAPROES_M<\/th>\n      <th>GRAPROES_F<\/th>\n      <th>PEA<\/th>\n      <th>PEA_M<\/th>\n      <th>PEA_F<\/th>\n      <th>PE_INAC<\/th>\n      <th>PE_INAC_M<\/th>\n      <th>PE_INAC_F<\/th>\n      <th>POCUPADA<\/th>\n      <th>POCUPADA_M<\/th>\n      <th>POCUPADA_F<\/th>\n      <th>PDESOCUP<\/th>\n      <th>PDESOCUP_M<\/th>\n      <th>PDESOCUP_F<\/th>\n      <th>PSINDER<\/th>\n      <th>PDER_SS<\/th>\n      <th>PDER_IMSS<\/th>\n      <th>PDER_ISTE<\/th>\n      <th>PDER_ISTEE<\/th>\n      <th>PDER_SEGP<\/th>\n      <th>P12YM_SOLT<\/th>\n      <th>P12YM_CASA<\/th>\n      <th>P12YM_SEPA<\/th>\n      <th>PCATOLICA<\/th>\n      <th>PNCATOLICA<\/th>\n      <th>POTRAS_REL<\/th>\n      <th>PSIN_RELIG<\/th>\n      <th>TOTHOG<\/th>\n      <th>HOGJEF_M<\/th>\n      <th>HOGJEF_F<\/th>\n      <th>POBHOG<\/th>\n      <th>PHOGJEF_M<\/th>\n      <th>PHOGJEF_F<\/th>\n      <th>TVIVHAB<\/th>\n      <th>TVIVPAR<\/th>\n      <th>VIVPAR_HAB<\/th>\n      <th>VIVPAR_DES<\/th>\n      <th>VIVPAR_UT<\/th>\n      <th>OCUPVIVPAR<\/th>\n      <th>PROM_OCUP<\/th>\n      <th>PRO_OCUP_C<\/th>\n      <th>VPH_PISODT<\/th>\n      <th>VPH_PISOTI<\/th>\n      <th>VPH_1DOR<\/th>\n      <th>VPH_2YMASD<\/th>\n      <th>VPH_1CUART<\/th>\n      <th>VPH_2CUART<\/th>\n      <th>VPH_3YMASC<\/th>\n      <th>VPH_C_ELEC<\/th>\n      <th>VPH_S_ELEC<\/th>\n      <th>VPH_AGUADV<\/th>\n      <th>VPH_AGUAFV<\/th>\n      <th>VPH_EXCSA<\/th>\n      <th>VPH_DRENAJ<\/th>\n      <th>VPH_NODREN<\/th>\n      <th>VPH_C_SERV<\/th>\n      <th>VPH_SNBIEN<\/th>\n      <th>VPH_RADIO<\/th>\n      <th>VPH_TV<\/th>\n      <th>VPH_REFRI<\/th>\n      <th>VPH_LAVAD<\/th>\n      <th>VPH_AUTOM<\/th>\n      <th>VPH_PC<\/th>\n      <th>VPH_TELEF<\/th>\n      <th>VPH_CEL<\/th>\n      <th>VPH_INTER<\/th>\n      <th>TAM_LOC_2<\/th>\n      <th>POBTOT_Urbana<\/th>\n      <th>quintil<\/th>\n      <th>NIVELAPROB<\/th>\n      <th>NUMREN<\/th>\n      <th>CUO<\/th>\n      <th>cuo.2<\/th>\n    <\/tr>\n  <\/thead>\n<\/table>","options":{"columnDefs":[{"className":"dt-right","targets":[2,3,4,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36,37,38,39,40,41,42,43,44,45,46,47,48,49,50,51,52,53,54,55,56,57,58,59,60,61,62,63,64,65,66,67,68,69,70,71,72,73,74,75,76,77,78,79,80,81,82,83,84,85,86,87,88,89,90,91,92,93,94,95,96,97,98,99,100,101,102,103,104,105,106,107,108,109,110,111,112,113,114,115,116,117,118,119,120,121,122,123,124,125,126,127,128,129,130,131,132,133,136,137,138,139,140,141,142,143,144,145,146,147,148,149,150,151,152,153,154,155,156,157,159,161,162,163,164,165,166,167,168,169,170,171,172,173,174,175,176,177,178,179,180,181,182,183,184,185,186,187,188,189,190,191,192,193,194,195,196,197,198,199,200,201,202,203,204,205,206,207,208,209,210,211,212,213,214,216,218,220,222,224,226,227,228,230,232,234,236,238,240,242,244,246,248,250,252,254,256,258,260,262,264,266,268,270,271,272,273,274,275,276,277,278,279,280,281,283,284,285,286,287,288,289,290,291,292,293,294,295,296,297,299,302,303,306,308,310,311,312,313,314,317,318,319,320,321,322,323,324,325,326,327,328,329,330,331,332,333,334,335,336,337,338,339,340,341,342,343,344,345,346,347,348,349,350,351,352,353,354,355,356,357,358,359,360,361,362,363,364,365,366,367,368,369,370,371,372,373,374,375,376,377,378,379,380,381,382,383,384,385,386,387,388,389,390,391,392,393,394,395,396,397,398,399,400,401,402,403,404,405,406,407,408,409,410,411,412,413,414,415,416,417,418,419,420,421,422,423,424,425,426,427,428,429,430,431,432,433,434,435,436,437,438,439,440,441,442,443,444,445,446,447,448,449,450,451,452,453,454,455,456,457,458,459,460,461,462,463,464,465,466,467,468,469,470,471,472,473,474,475,476,477,478,479,480,481,482,483,484,485,486,487,488,489,490,491,492,493,494,495,496,497,498,499,500,501,502,503,504,505,506,508,510,511]},{"orderable":false,"targets":0}],"order":[],"autoWidth":false,"orderClasses":false}},"evals":[],"jsHooks":[]}</script><!--/html_preserve-->

```r
ggplot(dat_ingreso, aes(x=INGTOT)) +  
  geom_histogram(bins = 100) + 
  scale_x_log10()
```

<img src="01-intro_files/figure-html/unnamed-chunk-4-2.png" width="480" />

Pero quizá podemos usar otras variables más fácilmente medibles
para predecir el ingreso de un hogar. Por ejemplo, si consideramos el número
de focos en la vivienda:


```r
ggplot(dat_ingreso, aes(x = FOCOS, y = INGTOT)) + 
  geom_point() +
  scale_y_log10() + xlim(c(0,50))
```

<img src="01-intro_files/figure-html/unnamed-chunk-5-1.png" width="480" />

O el tamaño de la localidad:

```r
ggplot(dat_ingreso, aes(x = tamaño_localidad, y = INGTOT)) + 
  geom_boxplot() +
  scale_y_log10() 
```

<img src="01-intro_files/figure-html/unnamed-chunk-6-1.png" width="480" />


- En algunas encuestas se pregunta directamente el ingreso mensual del hogar. La
respuesta es generalmente una mala estimación del verdadero ingreso, por lo que
actualmente se prefiere utilizar aprendizaje para estimar a partir de otras
variables que son más fielmente reportadas por encuestados (años de estudio,
ocupación, número de focos en el hogar, etc.)



