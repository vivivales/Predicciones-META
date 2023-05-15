# Predicciones-META

Implementación de Modelos para pronóstico de acciones de META
Se descargan los datos de la acción de META entre el: 01-05-2016 al 28-01-2018 y se analizan los resultados.
Se indexan las fechas y transformamos su formato a tipo zoo. Se hace la partición del conjunto de entrenamiento y test en proporciones de 95% y 5% respectivamente quedando con 22 datos para prueba y 417 para entrenamiento.
La función Tsfeatures nos muestra que para el periodo analizado la serie presenta una tendencia fuerte y positiva, sin picos muy prominentes, alta tendencia lineal con una curvatura pronunciada y baja entropía.
 

1.	ENTRENAMIENTO DE LOS MODELOS

1.1.	MODELO MARS
Al analizar los rezagos para observar cuáles son los de mayor significancia encontrando que el gráfico nos muestra que el rezago 8 es significativo, por lo que para mayor facilidad generamos los primeros 8 rezagos. 

Se entrena el modelo y encontramos que se seleccionaron 2 de 3 términos y 1 de los 9 predictores. De los rezagos únicamente se utilizó el primero. 

El gráfico izquierdo nos muestra que el modelo genera un ajuste muy cercano con los datos de entrenamiento. En cuanto a los datos de prueba encontramos un RMSE de 5.49 y un MAE de 4.3

Dentro de las ventajas que encontramos en este tipo de modelos, vemos su alta capacidad de interpretabilidad la cual nos permite a través de la función Hinge identificar tanto el intercepto como el punto de corte. Otra de las ventajas es que los Modelos MARS tienen la capacidad de modelar relaciones lineales y son altamente flexibles. Una de las desventajas está en que no es posible calcular un intervalo de confianza directo para el modelo.

1.2.	RED NEURONAL LSTM
Se transforman los datos con ayuda de TimeseriesGenerator para generar las muestras y se entrena la red con los parámetros indicados (200 neuronas, función activación “relu”, optimizador “adam”, función de pérdida “mse”, 30 épocas y una ventana de 10.
Los resultados nos muestran un RMSE de 5.89 y un MAE de 5.06. Dentro de las ventajas que encontramos en este método están la capacidad de modelar dependencias a largo plazo, si bien para este ejemplo únicamente contamos con datos de menos de 2 años, el modelo tiene la capacidad de aprender de manera incremental a lo largo del tiempo lo cual es adecuado para el manejo de series de tiempo. Una de las desventajas que encontramos es que su desempeño depende directamente del ajuste de los parámetros, lo cual puede cambiar en ocasiones de manera significativa sus resultados. Para este caso en donde teníamos unos parámetros definidos, sin embargo se habría podido explorar el comportamiento de esta red con una función de activación lineal o con otro optimizador. 
1.3.	RANDOM FOREST
 Al entrenar el modelo y calibrar los hiperparámetros del modelo se encuentra que el mejor desempeño por mse está en el modelo con 10 lags, un max_depth: 10 y n_estimators: 100. 
Este modelo tiene una gran ventaja y es la identificación de los features con mayor importancia, para este caso obtuvimos que son los primeros 4 rezagos son lo que mayor contribuyen a la predicción. Adicionalmente, es escalable y puede manejar grandes conjuntos de datos, lo cual permite la posibilidad de ampliar la ventana de tiempo en la cual se analiza la serie para obtener mayor información y lograr una mejor capacidad de pronosticar a futuro sus resultados. 
Con esta información entrenamos el modelo y obtenemos un desempeño con un RMSE de 6.3 y un MAE de 5.08. Una de las desventajas de estos modelos es el riesgo de caer en sobreajuste, lo cual se puede presentar cuando se utilizan conjuntos de datos pequeños. Adicionalmente, pierde capacidad de interpretación ya que es un modelo altamente complejo.
1.4.	SSA
Una de las ventajas de este modelo es su capacidad de separar las señales de una series de tiempo identificando su tendencia y estacionalidad de manera separada. Adicionalmente, no requiere estacionareidad y es muy fácil de impelmentar. Aprovechando esta ventaja, se aplica a la serie de META y se transforman los datos a formato ts. Observando en el scree plot, se puede ver un decaimiento de los log-valores singulares hasta el número 12. Adicionalmente, la tendencia puede estar representada por los vectores 1 al 3, aunque el primer componente es el que aporta la mayor parte de la variación (para la tendencia). Los pares de vectores que se usaron para explicar la estacionalidad fueron los 8 y 9 y 10 y 11 respectivamente.
 Al reconstruir la serie obtenemos el gráfico del lado derecho que nos muestra su comportamiento en las predicciones con un RMSE de 8.3 y un MAE de 7.02. Una de las desventajas que tiene este modelo es que es muy sensible al ruido. Además, la selección de parámetros debe hacerse de manera manual lo cual implica que debe seleccionarse cuidadosamente los parámetros a asignar en especial para reconstruir la serie en su tendencia y estacionalidad.

2.	PREDICCIONES A 10 DÍAS














3.	CONCLUSIONES
 	MARS	LSTM	SKForecaster	SSA
RMSE	5,49	5,89	6,31	8,48
MAE	4,3	5,06	5,08	7,07
El modelo que presentó un mejor desempeño en relación con las métricas de desempeño calculadas para todos fue el modelo MARS. Al ser una serie que tiene un alto nivel de linealidad y baja entropía, los modelos paramétricos tipo Model Based pueden capturar muy bien los patrones de la serie. Adicionalmente, no sacrifican la interpretabilidad y logran buenos desempeños. El modelo MARS además por su alta flexibilidad y eficiencia computacional, logró ajustarse muy fácilmente al conjunto de datos. 
Las redes neuronales, por su capacidad adaptativa pueden también presentar un muy buen desempeño, como vemos fue el segundo modelo en ranking con mejor desempeño. Sin embargo, la limitación que teníamos con los parámetros del modelo no permitió revisar si era posible alcanzar un mejor desempeño en este tipo de modelos. El SKForecaster es un modelo que funciona muy bien cuando tiene información de variables exógenas, en este caso, la única variable con la que contábamos eran los precios de la acción. Y en cuanto al SSA, la definición manual de los parámetros y el criterio del analista quizás puede sesgar un poco el resultado por la selección de vectores que conforman la estacionalidad y tendencia, para la reconstrucción de la serie.
