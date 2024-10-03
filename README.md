# LABORATORIO 3-EMG

La electromiografía es ampliamente utilizada en el ámbito de la ingeniería biomédica, ya que con esta, se mide la respuesta eléctrica rápida del músculo cuando se contrae. Esta técnica se centra en la actividad eléctrica generada por las unidades motoras durante contracciones rápidas y mínimas, permitiendo observar cómo responde rápidamente a estímulos eléctricos o a la activación voluntaria.Aunque la EMG puede proporcionar información sobre la fatiga muscular y cambios en la actividad eléctrica durante contracciones sostenidas, su enfoque principal es en las respuestas rápidas y dinámicas de los músculos. Con esto, la EMG nos permite, desarrollar dispositivos que a través de esta señal, activando diferentes mecanismos, moviendo por ejemplo; una prótesis biónica de mano. Por esta razón, es de vital importancia saber cómo se registra, se filtra y procesa la señal electromiográfica digitalmente.


En esta práctica se habla del registro y filtrado de dicha señal, para ello se utilizó un sistema de adquisición de datos STM32 y un módulo de captación de la señal electromiográfica AD8232, con este montaje se realizó la obtención de la señal electromiográfica del músculo flexor superficial de los dedos de un paciente de prueba, el cual realizó contracción del brazo hasta llegar a la fatiga muscular. Estos datos fueron guardados en un archivo txt. El cual se subió posteriormente en colab (como se puede observar a continuación) con el fin de graficar, filtrar y procesar la señal.

![image](https://github.com/user-attachments/assets/47df8b88-1503-44fd-9021-3ed87aa7da5e)


La señal tiene una frecuencia de muestreo de:100Hz  y un tiempo de muestreo de:1ms, en la gráfica de la señal se observan 24 repeticiones realizadas en 10s.

Teniendo esto en cuenta, se filtra digitalmente, para ello se utiliza un filtro butterworth, se eligió este filtro porque proporciona una respuesta en frecuencia muy uniforme hasta la frecuencia de corte, lo que significa que no introduce ondulaciones significativas en esta banda. A continuación se muestran los cálculos para encontrar el orden del filtro, el cual determina las características de la respuesta en frecuencia y entre mayor orden, más se aproxima a una frecuencia ideal de filtrado. Además, se calcularán las frecuencias de corte, la cual delimita el rango de frecuencias que se permiten pasar.
Para el diseño de este filtro se tuvo en cuenta las frecuencias alta y baja. el cual es el rango que queremos que pase. 10Hz y 250Hz, frecuencia baja y alta respectivamente. Finalmente se establece el factor de calidad de 1, ya que permite que la señal relevante se transmita con suficiente fidelidad, mientras se minimizan las distorsiones y el ruido.

 ![image](https://github.com/user-attachments/assets/d1c05308-76af-4b4c-aab0-2d4a9802d970)


Teniendo la señal filtrada se realiza un aventanamiento, el cual permite obtener un espectro más limpio y representativo de la señal, disminuyendo las discontinuidades en la señal.  Ayudando así al análisis de la misma, para ello se utilizó la ventana Hanning. 
Se implementó esta ventana debido a sus ventajas, entre ellas:
Da menor importancia a los datos al principio y al final del segmento analizado, generando que la señal se enfoque en los potenciales de acción, también suaviza las discontinuidades en la señal, además es fácil de implementar y calcular, debido a que existen diferentes bibliotecas para la implementación, además de poderse modificar el tamaño a partir de la cantidad de ventanas necesarias, por otro lado separar componentes frecuenciales que tienen niveles significativamente diferentes a la señal. 
Esta Ventana está definida matemáticamente mediante la siguiente fórmula:

![image](https://github.com/user-attachments/assets/f18fd90f-ae78-47b6-bd05-2ee1f69d6617)


Según esto, la forma de la ventana utilizada es senoidal, en python se implementó, np.hanning, donde se define el tamaño de la ventana siendo de 250 puntos. Este valor es 2.5 veces la frecuencia de muestreo, permitiendo un buen análisis espectral al tener gran número de puntos tomados en cada contracción muscular. 
A continuación se muestra la comparativa entre las gráficas de la señal filtrada con la señal aventanada. 

![image](https://github.com/user-attachments/assets/32a37341-6d5a-41df-a643-fef3d805b993)


Para realizar un análisis más detallado de la señal, se realizó una transformada al dominio de la frecuencia, y con esta se encontraron los datos estadísticos de la señal, como la frecuencia dominante, frecuencia  media de cada ventana y la desviación estándar.

En la frecuencia dominante se representa el componente de frecuencia que tiene mayor influencia en el comportamiento general de la señal, en este ejercicio de electromiografía se refiere a la mayor cantidad de energía en determinada frecuencia, en este caso ventana 20 con un valor de aproximadamente 15000dB.

![image](https://github.com/user-attachments/assets/dec69dba-09e1-40b0-a898-ff3af70e685b)

La frecuencia media hace referencia al promedio de las frecuencias de cada ventana realizada, este valor es utilizado posteriormente para realizar la prueba de hipótesis y verificar que entre cada ventana existan cambios significativos para determinar que realmente se llegó a la fatiga.  

La desviación estándar, nos ayuda a determinar que tan dispersos están los datos de la señal de la media de la misma, así sabemos que tan confiable es la toma de datos, y en este caso es de 0.68 aproximadamente. Lo cual no indica la confiabilidad de la señal, ya que es un valor reducido.

Contando con estos valores, culminamos la práctica con un test de hipótesis, el cual es un procedimiento estadístico que se utiliza para evaluar afirmaciones o suposiciones sobre una población basándose en datos obtenidos de una muestra. Este proceso implica comparar dos hipótesis: la hipótesis nula (H0) y la hipótesis alternativa (H1). 
La hipótesis nula(H0):Es la afirmación que se quiere probar y generalmente establece que no hay efecto o diferencia.
Hipótesis Alternativa (H1): Es lo que se desea demostrar, afirmando que existe un efecto o una diferencia.
Valor p: Es la probabilidad de obtener un resultado tan extremo como el observado, mide la evidencia en contra de la hipótesis nula. Un valor bajo, generalmente menor al nivel de significancia denotado en 0.05, indica que hay evidencia para rechazar la hipótesis nula. Y  un valor p alto sugiere que los resultados son consistentes con la hipótesis nula, lo que implica que no hay suficiente evidencia para rechazarla.
El valor P calculado en el código de python de las ventanas dio como resultado: 0.99, superando el nivel de significancia, esto quiere decir que la hipótesis nula no se rechaza, en nuestro contexto, quiere decir que en las ventanas no hubo un cambio significativo en el valor de los promedios de las frecuencias, para determinar que el músculo llegó a fatiga.

conclusiones

Como conclusión se debe mencionar la importancia de la filtración por diferentes métodos en una señal bio medica, ya sea para la miografía u otras, ya que el entendimiento de esta se puede describir el estado de la persona a estudiar, además si se detallan los potenciales de acción con respecto al movimiento se puede cuantificar el estado del musculo.

Por otro lado se tiene que mencionar que gracias a los filtros digitales se puede obtener una mayor precisión a la hora de eliminar componentes no deseados, ya que estos filtros se pueden generar por el orden necesario para la señal a filtrar, por otro lado también se debe mencionar el aventanamiento, el cual es fácil de aplicar para este tipo de señales, además de ser modificable para la adaptación de cada potencial y generar una ponderación con los datos no necesario, destacando la información mas relevante.
