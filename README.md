# Laboratorio 2: Estimación del nivel de estrés mediante GSR

## Integrantes
* Laura Valentina Velásquez Castiblanco (5600846)
* Carlos Felipe Moreno Guzmán (5600881)
* Juan Andrés Mateus Durán (5600787)

## Objetivos

### Objetivo General
Desarrollar un sistema vestible capaz de estimar el nivel de estrés de una persona a partir de la respuesta galvánica cutánea (GSR).

### Objetivos Específicos
- Identificar las componentes estacionaria y transitoria de la señal de GSR.
- Diseñar un sistema capaz de capturar de forma continua e inalámbrica las variaciones de la conductancia cutánea.
- Clasificar el nivel de estrés percibido por el sujeto a partir del cambio porcentual de la GSR respecto a un basal individual.

> ### Parte A
### **1. Revisión de la literatura**

#### **Actividad electrodérmica (EDA) y respuesta galvánica cutánea (GSR)**

La actividad electrodérmica (EDA) agrupa todos los fenómenos eléctricos que ocurren a nivel de la piel, entre ellos las variaciones de su capacidad para conducir electricidad, conocida como conductancia cutánea [1]. Esta conductancia no es constante: presenta un nivel basal o estacionario (SCL) sobre el cual se superponen cambios rápidos y transitorios llamados respuestas de conductancia cutánea o Skin Conductance Response (SCR). Cada SCR se caracteriza por un ascenso súbito seguido de un retorno mucho más lento al valor basal, con parámetros típicos como latencia, tiempo de subida, amplitud y tiempo de recuperación al 50 % [4].

Fisiológicamente, estos cambios se explican por la activación del sistema nervioso simpático sobre las glándulas sudoríparas ecrinas: al aumentar la actividad simpática se incrementa la secreción de sudor y, con ella, la conductividad eléctrica de la piel [1]. Por esta razón, la EDA se considera un indicador no invasivo y de bajo costo del estado de activación (arousal) autonómico, sensible tanto a estímulos emocionales como a estímulos físicos.

Distintos estudios han demostrado que la GSR responde de forma medible ante estímulos que producen dolor o carga cognitiva. Por ejemplo, se ha reportado que estímulos térmicos dolorosos generan incrementos consistentes en la conductancia cutánea, correlacionados con la intensidad percibida del dolor [2]. De manera similar, se ha encontrado que las respuestas de conductancia cutánea permiten discriminar entre distintas intensidades de un estímulo doloroso inducido experimentalmente, aunque con limitaciones en su capacidad de discriminación fina [3]. Estos hallazgos respaldan el uso de la GSR como indicador cuantitativo de activación fisiológica ante tareas que demandan esfuerzo o generan tensión, como ocurre durante la resolución de problemas bajo presión de tiempo.

La GSR también se ha aplicado en campos más amplios que la psicofisiología clásica: como señal de biorretroalimentación en el manejo de condiciones neurológicas [5], y como medida de referencia en estudios de toma de decisiones y comportamiento bajo incertidumbre. Esta versatilidad, más la sencillez de su instrumentación (dos electrodos y un circuito de bajo voltaje) es lo que hace de la GSR una señal atractiva para un dispositivo vestible orientado a estimar el nivel de estrés percibido por una persona.

> ### Parte C

### 1. Procedimiento General

El dispositivo vestible se construyó con dos electrodos, elaborados a partir de la parte superior (terminal metálico) de una pila, ubicados sobre la palma de la mano y conectados a un divisor de tensión resistivo ($R = 68\ \text{k}\Omega$) alimentado a $3.3\ \text{V}$ desde el ESP32, cuyo pin analógico (resolución de $12\ \text{bits}$) midió la caída de voltaje asociada a la conductancia cutánea. El microcontrolador se configuró como punto de acceso WiFi ("GSR_ESP32"), sirviendo una página web que se actualiza cada $300\ \text{ms}$ mediante peticiones AJAX al endpoint `/datos`, permitiendo la visualización inalámbrica en tiempo real desde un celular conectado a la red del ESP32, sin necesidad de cable ni de aplicación adicional.

Al energizar el sistema se ejecutó una rutina de calibración de $15\ \text{s}$, durante la cual el sujeto permaneció sentado y en reposo, promediando la conductancia registrada para establecer la línea base (SCL). A partir de este basal, el firmware calculó en cada ciclo de muestreo el cambio porcentual de conductancia y lo clasificó en tres niveles (poco estrés, estrés moderado, estrés elevado) según los umbrales definidos en la Parte B ($5\ \%$ y $20\ \%$).

Sobre esta base se evaluó el comportamiento del dispositivo ante tres condiciones: (I) una inspiración profunda súbita con el sujeto sentado, (II) el paso de sedente a bípedo (puesta en pie) y (III) actividad física de impacto (skipping), con el fin de contrastar la respuesta ante distintos tipos y magnitudes de activación simpática. Finalmente, se registró el sistema operando de forma completamente inalámbrica, visualizando en el celular el nivel de estrés estimado mientras el sujeto resolvía una tarea que demandaba concentración.

> [!NOTE]
> La calibración de $15\ \text{s}$ con el sujeto en reposo es el paso clave del sistema, debido a que todos los umbrales de clasificación posteriores (POCO ESTRÉS, ESTRÉS MODERADO, ESTRÉS ELEVADO) se calculan como cambio porcentual respecto a este basal individual, y no como valores absolutos.

### 2. Resultados Obtenidos

**Inspiración súbita:** Partiendo de un basal de aproximadamente $0.68\ \text{V}$, la señal mostró oscilaciones leves asociadas al ciclo respiratorio en reposo hasta el segundo $16$, momento en el que se presentó un ascenso pronunciado hasta un pico de $0.905\ \text{V}$ ($t \approx 19\ \text{s}$), seguido de un retorno lento y monotónico hacia $0.75\ \text{V}$ al finalizar el registro, sin alcanzar el valor basal en los $30\ \text{s}$ observados.

**Puesta en pie:** La señal partió de $1.10\ \text{V}$, con un breve descenso inicial y un primer hombro entre los segundos $3$ y $6$ ($\approx 1.20\ \text{V}$), para luego ascender de forma pronunciada hasta un pico de $1.807\ \text{V}$ ($t \approx 14\ \text{s}$). La recuperación mostró una meseta intermedia ($\approx 1.66\ \text{V}$ entre $t = 19\text{–}22\ \text{s}$) antes de continuar el descenso hasta $1.50\ \text{V}$ al final del registro.

**Correr (skipping):** La señal partió de $0.787\ \text{V}$ y mostró un patrón compuesto, con un primer ascenso hasta $0.838\ \text{V}$ ($t \approx 4\text{–}5\ \text{s}$), una meseta cercana a $0.887\ \text{V}$ ($t \approx 7\text{–}9\ \text{s}$), un pico principal de $0.915\ \text{V}$ ($t \approx 12\text{–}13\ \text{s}$), un descenso parcial, un segundo pico secundario de $0.907\ \text{V}$ ($t \approx 21\ \text{s}$) y una caída final hasta un mínimo de $0.835\ \text{V}$ ($t \approx 27\ \text{s}$), cerrando en $0.877\ \text{V}$.

**Monitoreo inalámbrico durante tarea de concentración:** El sistema operó de forma completamente inalámbrica de extremo a extremo (sensor → ESP32 → WiFi → navegador del celular), mostrando en tiempo real, a través de la interfaz web, el nivel de activación estimado junto con los valores de conductancia, basal y resistencia cutánea calculados por el firmware mientras el sujeto resolvía la tarea de concentración.

**Cambio porcentual de conductancia por condición**

| Condición | G basal ($\mu\text{S}$) | G pico ($\mu\text{S}$) | Δ Conductancia (%) |
|---|:---:|:---:|:---:|
| Inspiración súbita | $3.82$ | $5.56$ | ≈ 45.6 % |
| Puesta en pie | $7.35$ | $17.80$ | ≈ 142.1 % |
| Correr (skipping) | $4.61$ | $5.64$ | ≈ 22.5 % |

> [!NOTE]
> Los valores de conductancia de esta tabla se calcularon aplicando la misma fórmula del firmware ($G = V / [R \cdot (V_{CC}-V)] \times 10^6$) a las señales de voltaje registradas por MATLAB mediante el endpoint /acquire.

### 3. Análisis de Resultados

**3.1 Comparación fisiológica entre condiciones**

Al expresar las tres condiciones en términos de conductancia ($\mu\text{S}$) en lugar de voltaje crudo, se hace evidente que la puesta en pie generó, por un margen amplio, la mayor activación relativa ($\approx 142\ \%$). Esto es consistente con que este estímulo combina dos mecanismos simpáticos simultáneos: por un lado, la respuesta barorreceptora ante el cambio ortostático, que exige una activación simpática rápida para mantener la presión de perfusión cerebral al ponerse de pie [7], y por otro, la anticipación motora y el esfuerzo postural del propio movimiento. Ambos mecanismos convergen sobre las glándulas sudoríparas ecrinas de inervación simpática colinérgica [5], amplificando la respuesta de conductancia muy por encima de la generada por la inspiración aislada.

La inspiración súbita, por su parte, al ser un estímulo puramente autonómico —sin componente motor ni cambio postural—, generó una respuesta más contenida ($\approx 46\ \%$) pero con la morfología más nítida de las tres: una SCR bien definida, de ascenso rápido y recuperación lenta, coherente con el patrón clásico descrito para la actividad electrodérmica ante un estímulo discreto [8].

**3.2 Comportamiento de la señal durante la actividad física**

A diferencia de la SCR única registrada durante la inspiración, el skipping produjo un registro con varios picos y valles a lo largo de los 30 s (t ≈ 4-5 s, 7-9 s, 12-13 s y 21 s), correspondientes al carácter sostenido y rítmico del ejercicio: cada salto genera un nuevo impulso de activación antes de que el anterior termine de disiparse, lo que da lugar a esta sucesión de picos en lugar de un único ascenso y descenso como en la inspiración. Esto indica que el sistema logró mantener el registro de la señal durante todo el ejercicio, sin interrupciones ni pérdida de datos, pese a tratarse de la prueba con mayor movimiento de las tres. El resultado confirma que el montaje sobre la palma de la mano soporta el desplazamiento del brazo y el impacto repetido de los saltos sin que la adquisición se detenga o se sature.

**3.3 Análisis 1 — Aplicabilidad del sistema en entornos cotidianos**

El sistema opera de forma completamente inalámbrica y se visualiza en un celular sin necesidad de instalar ningún software adicional, lo que permite emplearlo en distintos escenarios cotidianos como el trabajo de oficina, la actividad académica en aula o tareas domésticas. En estos contextos, el objetivo es detectar cambios de conductancia asociados a carga cognitiva o emocional, tal como ocurrió en el registro obtenido durante la tarea de concentración, donde el sistema identificó y comunicó en tiempo real el nivel de activación estimado. Esto demuestra que el dispositivo puede usarse como una herramienta de monitoreo continuo del estado autonómico del usuario en su día a día, sin requerir equipos adicionales ni intervención de un tercero para interpretar los resultados.

**3.4 Análisis 2 — Alcance y proyección hacia el monitoreo de estrés neonatal**

El principio de medición implementado (electrodos de contacto cutáneo acoplados a un divisor resistivo, con umbrales de clasificación relativos a un basal individual) es teóricamente extrapolable a la población neonatal, dado que la actividad electrodérmica ha sido empleada en unidades de cuidado neonatal como indicador de dolor y estrés [5]. El dispositivo construido en esta práctica cumple con ese mismo esquema de funcionamiento: adquiere la señal, calcula un basal individual y clasifica el nivel de activación a partir de él.

Su extensión hacia esta población requeriría, no obstante, tres adaptaciones específicas: el rediseño de los electrodos con dimensiones y materiales biocompatibles apropiados para la piel neonatal; la recalibración de los umbrales de clasificación con base en la fisiología electrodérmica propia de esta población; y la validación de la seguridad eléctrica del dispositivo conforme a la normativa pediátrica correspondiente. Estas modificaciones constituyen ajustes sobre una arquitectura ya validada, más que un rediseño integral del sistema.

En consecuencia, el sistema desarrollado constituye una prueba de concepto sólida del principio de medición empleado, con potencial de extensión hacia aplicaciones especializadas como el monitoreo del estrés neonatal.

### 4. Preguntas para la Discusión

**Pregunta 1: ¿A qué se debe que una inspiración profunda incremente la magnitud de la respuesta galvánica cutánea (GSR)?**

Esto se debe a la particularidad de la inervación de las glándulas sudoríparas ecrinas: reciben únicamente fibras simpáticas, pero de tipo colinérgico, por lo que cualquier descarga simpática se traduce directamente en más sudoración y, por tanto, en mayor conductancia cutánea [5]. Una inspiración profunda y súbita activa mecanorreceptores de estiramiento pulmonar, cuya señal asciende al tronco encefálico y genera una breve activación simpática generalizada, reforzada además por el componente de alerta que suele acompañar a una inhalación brusca [6]. Esto concuerda con lo observado: el ascenso de la señal fue rápido, propio de la latencia corta de una SCR, mientras que el retorno al basal fue mucho más lento, ya que depende de procesos pasivos (difusión y evaporación del sudor) y no de un control neural activo como el ascenso [8].

**Pregunta 2: ¿Cuáles serían las ventajas y desventajas de utilizar la GSR como indicador de estrés?**

*Ventajas:* Es una señal económica y sencilla de instrumentar (como se vio en esta práctica, bastan un divisor resistivo y un ADC), no invasiva y de fácil colocación. Al depender solo de inervación simpática, sin la influencia moduladora del parasimpático que sí afecta a otras señales como la frecuencia cardiaca, ofrece una lectura relativamente directa del nivel de activación simpática [5], con una latencia corta que permite detectar eventos de activación casi en tiempo real, como se evidenció durante la tarea de concentración realizada en esta práctica.

*Desventajas:* Al ser una señal de contacto, su calidad puede variar según las condiciones de sujeción del electrodo, y al no diferenciar la causa de la activación simpática, un mismo incremento de conductancia puede deberse a estrés, esfuerzo físico, sorpresa o excitación positiva [6]. A esto se suma la variabilidad interindividual en la reactividad de las glándulas sudoríparas [5], que hace necesario calibrar el basal en cada sujeto, como se hizo en esta práctica, para obtener lecturas comparables dentro de una misma persona. Asimismo, al tratarse de una señal periférica de latencia relativamente lenta en su fase de recuperación, la GSR no resulta adecuada para el seguimiento de eventos de muy corta duración, siendo más útil en aplicaciones que buscan tendencias generales de activación que en la detección precisa de estímulos puntuales muy próximos entre sí [8].

### 5. Conclusiones

El dispositivo desarrollado permitió capturar y clasificar en tiempo real, de forma completamente inalámbrica, las variaciones de la respuesta galvánica cutánea ante distintos tipos de estímulos, cumpliendo el objetivo general de la práctica. La comparación entre condiciones evidenció que la magnitud de la respuesta refleja la naturaleza de cada estímulo: estímulos autonómicos puros (inspiración) generan respuestas proporcionales y bien definidas, mientras que estímulos que combinan activación simpática con movimiento (puesta en pie, correr) producen respuestas más amplias y dinámicas, enriqueciendo la lectura del estado autonómico del sujeto.

La GSR se confirmó como un indicador fisiológicamente coherente y de reacción rápida ante la activación simpática, validando el principio de medición implementado en el sistema. Su mayor fortaleza radica en el monitoreo relativo de un mismo sujeto respecto a su propio basal, ofreciendo una lectura confiable y en tiempo real del estado autonómico tanto en contextos de baja movilidad, como el trabajo de oficina o el estudio, como durante actividad física, según se evidenció en el registro obtenido durante el skipping.

### Referencias Bibliográficas

[5] W. Boucsein, *Electrodermal Activity*. Nueva York, NY, Estados Unidos: Springer Science & Business Media, 2012.

[6] H. D. Critchley, "Electrodermal Responses: What Happens in the Brain," *The Neuroscientist*, vol. 8, no. 2, pp. 132–142, 2002. https://doi.org/10.1177/107385840200800209.

[7] A. C. Guyton y J. E. Hall, *Textbook of Medical Physiology*, 13.ª ed. Filadelfia, PA, Estados Unidos: Elsevier, 2016.

[8] B. Figner y R. O. Murphy, "Using skin conductance in judgment and decision making research," en *A Handbook of Process Tracing Methods for Decision Research*, M. Schulte-Mecklenbeck, A. Kuehberger y R. Ranyard, Eds. Nueva York, NY, Estados Unidos: Psychology Press, 2011, pp. 163–184.
