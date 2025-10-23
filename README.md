# Procesamiento de una señal electromiográfica asociada a la fatiga muscular

# Introducción

El electromiograma (EMG) es una técnica utilizada para registrar la actividad eléctrica generada por los músculos durante su contracción. Esta actividad, conocida como bioseñal mioeléctrica, se origina a partir de los potenciales de acción de las fibras musculares y permite analizar el funcionamiento neuromuscular. Existen dos tipos principales de EMG: el superficial, que emplea electrodos colocados sobre la piel, y el intramuscular o de aguja, que registra la señal directamente dentro del músculo.
En el caso del EMG de superficie, los electrodos activos se ubican sobre la piel del músculo a estudiar, mientras que un electrodo de referencia o tierra se coloca en una zona eléctricamente neutra del cuerpo. La señal EMG se obtiene como la diferencia entre las mediciones de los electrodos activos y refleja la actividad eléctrica subyacente de las fibras musculares.
El registro de esta señal depende de varios factores fisiológicos y geométricos, como la conductividad del tejido, la distancia entre los electrodos y la piel, y la posición relativa de las fibras musculares. La respuesta impulsiva del potencial de acción muscular puede modelarse mediante ecuaciones que relacionan la corriente generada en la fibra con la disposición de los electrodos sobre la superficie cutánea. Este modelo permite comprender cómo la geometría de captura influye directamente en la forma y amplitud de la señal registrada.

# Ventanas de Hanning y Hamming

Cuando se analiza una señal EMG (o cualquier señal biológica) en el dominio de la frecuencia, normalmente se divide en segmentos más pequeños llamados ventanas de tiempo. Esto se hace porque las señales biológicas son no estacionarias, es decir, sus características cambian con el tiempo (por ejemplo, al inicio o final de una contracción muscular).
Sin embargo, al aplicar directamente la Transformada de Fourier (FFT) sobre una porción finita de señal, pueden aparecer discontinuidades en los bordes de cada ventana, lo que introduce errores en el espectro conocidos como fugas espectrales.
Para reducir este efecto, se aplica una función ventana como la de Hanning o de Hamming que suaviza los extremos de la señal antes de transformarla.

# Diferencia entre Hanning y Hamming

Hanning suaviza más los bordes, es decir, menos “ruido” en el espectro, pero sacrifica un poco la resolución. Ideal para señales ruidosas o biológicas, como el EMG, 
donde interesa observar los cambios generales en la frecuencia. Hamming en cambio, suaviza menos, mantiene mejor la amplitud de las componentes, pero deja algo más de fuga. 
Útil cuando se necesita medir amplitudes exactas o comparar potencias entre frecuencias.

# Transformada de Fourier

La Transformada de Fourier (TF) es una herramienta matemática fundamental en el procesamiento de señales que permite analizar cómo se distribuye la energía o potencia de una señal en función de la frecuencia. En otras palabras, transforma una señal del dominio del tiempo —donde se observa cómo varía la amplitud con el tiempo— al dominio de la frecuencia, donde se identifican las componentes sinusoidales que la componen.
Cuando se aplica a una señal electromiográfica (EMG), la Transformada de Fourier permite obtener su espectro de frecuencias, mostrando qué componentes frecuenciales predominan durante la actividad muscular. Dado que la señal EMG es una mezcla de potenciales de acción generados por múltiples fibras musculares, su espectro refleja la complejidad y la intensidad de la contracción muscular.

# Aplicación en el análisis EMG

En el estudio de la fatiga muscular, la Transformada de Fourier se aplica a segmentos o ventanas de la señal EMG para observar cómo cambia su contenido espectral con el tiempo.
Durante la contracción sostenida de un músculo:

Las frecuencias altas tienden a disminuir a medida que el músculo se fatiga.
La frecuencia mediana del espectro se desplaza hacia valores más bajos.
Este comportamiento es un indicador confiable de fatiga muscular, ya que refleja la disminución en la velocidad de conducción de las fibras musculares.

# Adquisición de datos:

El proceso que se llevo a cabo para adquirir las señales EMG del músculo del antebrazo fue en primer lugar poner los electrodos en la superficie de los músculos a evaluar. El sistema de adquisición que se utiliza fue por medio del ESP32, el cual permite registrar la actividad eléctrica arrojada por el músculo durante la contracción continua hasta la fatiga. Los electrodos fueron conectados al módulo y al ESP32, por medio de C++ se adquirió la señal con una frecuencia de muestreo de 1000 Hz por 30 segundos, se adquirieron 120,000 muestras en total, para capturar adecuadamente la señal sin perder datos importantes de la misma, monitoreando el músculo en tiempo real para corroborar que los datos adquiridos eran correctos.

```cpp
const int pinEMG = 34;
void setup() {
  Serial.begin(115200);
}
void loop() {
  int valor = analogRead(pinEMG);
  Serial.println(valor);
  delayMicroseconds(1000); // ~1 kHz 
}
```

# Procesamiento de la señal

Para el procesamiento de la señal, en primer lugar se cargaron los datos, leyendo la señal por medio de emg_data.txt , en donde se vio la siquiente señal EMG.
![image](https://github.com/felipeacosta-m/Lab4-electromiograficas-EMG/blob/218374babc0bd294ab3fe1c2a55f3b79e4ec4d1a/Se%C3%B1al%20original.png)

Posteriormente se le aplico tanto un filtro pasa-bajas y un pasa-altas para obtener una señal más limpia:
![image](https://github.com/felipeacosta-m/Lab4-electromiograficas-EMG/blob/608ab3b3ab8cb94d916754076b1dfacb209c5ec4/Filtro%20pasa-bajas.png)
![image](https://github.com/felipeacosta-m/Lab4-electromiograficas-EMG/blob/608ab3b3ab8cb94d916754076b1dfacb209c5ec4/Filtro%20pasa-altas.png)

