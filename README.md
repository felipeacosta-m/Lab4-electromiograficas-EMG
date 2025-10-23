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

En la señal se detectan los picos de la señal, los cuales fueron 26 impulsos significativos, por esta razón se producen 26 ventanas alrededor de los picos que se detectan. El siguiente paso fue realizar los cálculos de la FFT para cada ventana y se almacenaron los espectros obtenidos. Además, se hicieron los debidos cálculos estadísticos por medio del siguiente código:

```cpp
# ======================================================================
# === SECCIÓN ESTADÍSTICOS ADICIONALES ===
# ======================================================================
print("   ESTADÍSTICOS DE LA SEÑAL EMG CRUDA")
print("===========================================")

# --- a. Media ---
media = np.mean(data)

# --- b. Desviación estándar ---
desviacion_std = np.std(data)

# --- c. Coeficiente de variación ---
coef_var = (desviacion_std / abs(media) * 100) if media != 0 else np.nan

# --- d. Histograma ---
plt.figure(figsize=(10, 5))
plt.hist(data, bins=50, color='skyblue', edgecolor='black', density=True)
plt.title("Histograma de la señal EMG cruda")
plt.xlabel("Amplitud (V)")
plt.ylabel("Densidad de probabilidad")
plt.grid(True)
plt.show()

# --- e. Función de probabilidad (PDF estimada) ---
counts, bin_edges = np.histogram(data, bins=50, density=True)
pdf = counts / np.sum(counts)
centros = (bin_edges[:-1] + bin_edges[1:]) / 2

plt.figure(figsize=(10, 5))
plt.plot(centros, pdf, color='darkred', linewidth=2)
plt.title("Función de Probabilidad (PDF) estimada - Señal cruda")
plt.xlabel("Amplitud (V)")
plt.ylabel("Probabilidad")
plt.grid(True)
plt.show()

# --- f. Curtosis ---
from scipy.stats import kurtosis
curtosis_val = kurtosis(data)

# === Mostrar resultados numéricos formateados ===
print(f"Media ......................: {media:.6f}")
print(f"Desviación estándar ........: {desviacion_std:.6f}")
print(f"Coeficiente de variación ...: {coef_var:.2f} %")
print(f"Curtosis ...................: {curtosis_val:.6f}")
print("===========================================")
```
Después de realizar el análisis espectral por ventanas sobre la señal EMG filtrada, se aplicó una prueba de hipótesis t pareada de dos colas utilizando la función ttest_rel() de la librería SciPy. Esta prueba se implementó para comparar las frecuencias medianas obtenidas en las ventanas iniciales y finales de la señal, con el fin de determinar si existe una disminución significativa asociada a la aparición de fatiga muscular.
Cada segmento temporal de 2 s (con un solapamiento del 50 %) fue procesado mediante el método de Welch para estimar la densidad espectral de potencia (PSD), y a partir de ella se extrajo la frecuencia mediana. Posteriormente, las frecuencias de la primera mitad de las ventanas se agruparon en grupo1 y las de la segunda mitad en grupo2.
La prueba t compara ambos conjuntos bajo la hipótesis nula de que sus medias son iguales. Se adoptó un nivel de significancia de α = 0.05 para evaluar si el cambio en la frecuencia mediana era estadísticamente relevante. Si el valor p obtenido es menor que α, se rechaza la hipótesis nula, concluyendo que existe una reducción significativa de la frecuencia mediana, lo que constituye evidencia de fatiga muscular. En caso contrario, no se detecta una diferencia estadísticamente significativa, aunque puede observarse una tendencia descendente en la frecuencia mediana a lo largo de la contracción.

```cpp
# === Prueba estadística ===
n_mitad = n_ventanas // 2
grupo1 = frecuencias_medianas[:n_mitad]
grupo2 = frecuencias_medianas[n_mitad:]
t_stat, p_val = ttest_rel(grupo1, grupo2)

print(f"\nFrecuencia mediana inicial: {np.mean(grupo1):.2f} Hz")
print(f"Frecuencia mediana final: {np.mean(grupo2):.2f} Hz")
print(f"p-valor: {p_val:.4f}")

if p_val < 0.05:
    print("✅ Cambio significativo en la frecuencia mediana → evidencia de fatiga muscular.")
else:
    print("⚠️ No hay cambio estadísticamente significativo.")
```

Dado que el estadístico de prueba no está en la región crítica, no hay suficiente evidencia para rechazar la hipótesis nula. Es decir, no podemos concluir que se haya alcanzado la fatiga con el nivel de significancia del 1%.

![image](https://github.com/felipeacosta-m/Lab4-electromiograficas-EMG/blob/a4841e101c48a3cad029ff68cc9ecf927fabdc6f/Prueba%20fatiga.png)

# Conclusiones

La adquisición de datos se pudo llevar a cabo de manera efectiva con una frecuencia de muestreo conveniente para el tiempo de fatiga muscular del individuo, dando acceso a una correcta captación de la señal EMG. El debido procesamiento de dicha señal permite evidenciar 26 picos significativos o de intereses necesarios para ser analizados, por medio de la aplicación de la FFT para poder realizar la extracción de características frecuenciales.
Por medio de la implementación de la transformada de Fourier se pudo observar detalladamente la estructura frecuencial de los impulsos adquiridos, de esta manera completar el análisis estadístico con el cálculo de la media y desviación estándar de las ventanas creadas, el análisis de la señal electromiográfica nos permitió observar en detalle la actividad eléctrica del músculo del antebrazo y detectar los momentos clave de contracción. Sin embargo, los resultados obtenidos sugieren que, dentro del tiempo evaluado, no hubo una diferencia lo suficientemente significativa como para concluir que se alcanzó la fatiga muscular.
