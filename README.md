# LABORATORIO 3-EMG

La electromiografía es ampliamente utilizada en el ámbito de la ingeniería biomédica, ya que con esta, se mide la respuesta eléctrica del músculo cuando se contrae, esto nos permite, desarrollar dispositivos que a través de esta señal, activan un mecanismo moviendo por ejemplo una prótesis biónica de mano. Por esta razón, es de vital importancia saber cómo se registra, se filtra y procesa la señal electromiográfica digitalmente.

En esta práctica se habla del registro y filtrado de dicha señal, para ello se utilizó un sistema de adquisición de datos STM32 y un módulo de captación de la señal electromiográfica AD8232, con este montaje se realizó la obtención de la señal electromiográfica del músculo bíceps de un paciente de prueba, el cual realizó flexión y extensión del brazo hasta llegar a la fatiga muscular. Estos datos fueron guardados en un archivo txt. El cual se subió posteriormente en colab (como se puede observar a continuación) con el fin de graficar, filtrar y procesar la señal.

![image](https://github.com/user-attachments/assets/47df8b88-1503-44fd-9021-3ed87aa7da5e)


La señal tiene una frecuencia de muestreo de:100Hz  y un tiempo de muestreo de:1ms, en la gráfica de la señal se observan 24 repeticiones realizadas en 10s.

Teniendo esto en cuenta, se filtra digitalmente, para ello se utiliza un filtro butterworth, se eligió este filtro porque proporciona una respuesta en frecuencia muy uniforme hasta la frecuencia de corte, lo que significa que no introduce ondulaciones significativas en esta banda. A continuación se muestran los cálculos para encontrar el orden del filtro, el cual determina las características de la respuesta en frecuencia y entre mayor orden, más se aproxima a una frecuencia ideal de filtrado. Además, se calcularán las frecuencias de corte, la cual delimita el rango de frecuencias que se permiten pasar.
Para el diseño de este filtro se tuvo en cuenta las frecuencias alta y baja. el cual es el rango que queremos que pase. 20Hz y 250Hz, frecuencia baja y alta respectivamente. Finalmente se establece el factor de atenuación en este caso -4dB, ya que permite que la señal relevante se transmita con suficiente fidelidad, mientras se minimizan las distorsiones y el ruido.

 ![image](https://github.com/user-attachments/assets/d1c05308-76af-4b4c-aab0-2d4a9802d970)


Teniendo la señal filtrada se realiza un aventanamiento, el cual permite obtener un espectro más limpio y representativo de la señal, disminuyendo las discontinuidades en la señal.  Ayudando así al análisis de la misma, para ello se utilizó la ventana Hanning. 
Se implementó esta ventana debido a sus ventajas, entre ellas:
Da menor importancia a los datos al principio y al final del segmento analizado. Esto suaviza las discontinuidades en la señal.
Es fácil de implementar y calcular.
Separar componentes frecuenciales que tienen niveles significativamente diferentes a la señal. 
Esta Ventana está definida matemáticamente mediante la siguiente fórmula:

![image](https://github.com/user-attachments/assets/134ce261-077b-4bcd-acb4-5898e68ccd30)

Según esto, la forma de la ventana utilizada es senoidal, en python se implementó, np.hanning, donde se define el tamaño de la ventana siendo de 50 puntos. Este valor es 50 veces la frecuencia de muestreo, permitiendo un buen análisis espectral al tener gran número de puntos tomados en cada contracción muscular. 
A continuación se muestra la comparativa entre las gráficas de la señal filtrada con la señal aventanada. 

![image](https://github.com/user-attachments/assets/201ad46d-eec3-46a1-b840-1a517d3ab098)

Para realizar un análisis más detallado de la señal, se realizó una transformada al dominio de la frecuencia, y con esta se encontraron los datos estadísticos de la señal, como la frecuencia dominante, frecuencia  media de cada ventana y la desviación estándar.

En la frecuencia dominante se representa el componente de frecuencia que tiene mayor influencia en el comportamiento general de la señal, en este ejercicio de electromiografía se refiere a la mayor cantidad de energía en determinada frecuencia, en este caso ventana 20 con un valor de aproximadamente 15000dB.

![image](https://github.com/user-attachments/assets/dec69dba-09e1-40b0-a898-ff3af70e685b)

La frecuencia media hace referencia al promedio de las frecuencias de cada ventana realizada, este valor es utilizado posteriormente para realizar la prueba de hipótesis y verificar que entre cada ventana existan cambios significativos para determinar que realmente se llegó a la fatiga.  
