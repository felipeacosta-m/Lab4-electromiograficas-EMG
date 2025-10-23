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

