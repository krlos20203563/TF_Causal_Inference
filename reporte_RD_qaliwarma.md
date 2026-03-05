---
output:
  html_document: default
  pdf_document: default
  word_document: default
---
# Impacto del Programa Qali Warma sobre la Anemia Infantil: Un Análisis de Regresión Discontinua

**Trabajo Final — Fundamentos de Inferencia Causal**  
**Q-LAB — Escuela de Verano 2026**

---

## Resumen

Este trabajo estima el efecto causal del programa de alimentación escolar **Qali Warma** sobre la prevalencia de **anemia** en niños de 33 a 39 meses de edad, utilizando una estrategia de **Regresión Discontinua (RD)**. La fuente de variación exógena es el umbral de elegibilidad del programa: los niños que tienen **36 meses o más** son elegibles para recibir el programa en los centros de educación inicial del Estado, mientras que los menores de 36 meses no lo son. Esta discontinuidad administrativa, que no depende de características individuales de los niños ni de sus hogares, permite construir un grupo de control válido y estimar el efecto causal del programa sobre la anemia.

El estimador de RD capta el **Efecto Local Promedio del Tratamiento (LATE)** en el umbral de 36 meses: compara la probabilidad de anemia de los niños justo por encima y justo por debajo de ese umbral, bajo el supuesto de que ambos grupos son, en promedio, comparables en todas sus características salvo en su exposición al programa. Las pruebas de robustez —incluyendo el test de densidad de McCrary, los tests de placebo en covariables y la sensibilidad al bandwidth— evalúan la validez de este supuesto.

---

## 1. Introducción

### 1.1 El programa Qali Warma

**Qali Warma** (en quechua: "niño vigoroso") es el programa de alimentación escolar del Estado peruano, administrado por el Ministerio de Desarrollo e Inclusión Social (MIDIS). Su propósito es garantizar el servicio de alimentación a los niños y niñas que asisten a instituciones educativas públicas a lo largo de todo el año escolar, con el fin de mejorar su atención, asistencia y hábitos alimenticios.

El programa opera en dos niveles: **inicial** y **primaria**. En el nivel inicial, la elegibilidad comienza a los **36 meses de edad** (3 años cumplidos), que es la edad mínima para ingresar a los Centros de Educación Inicial (CEI) o Programas No Escolarizados de Educación Inicial (PRONOEI) que reciben las raciones del programa. Esta regla de corte por edad es la base de nuestra estrategia empírica.

El programa entrega a los niños elegibles una ración alimentaria diaria durante los días del calendario escolar. Las raciones están diseñadas para cubrir entre el 20% y el 30% de los requerimientos nutricionales diarios del niño, con especial atención al aporte de hierro, zinc y otros micronutrientes clave para prevenir la anemia. En ese sentido, existe una plausibilidad biológica y nutritiva para esperar que el programa reduzca la prevalencia de anemia en los niños beneficiarios.

### 1.2 La anemia infantil en el Perú como problema de política pública

La anemia infantil es uno de los principales problemas de salud pública del Perú. Según la Encuesta Demográfica y de Salud Familiar (ENDES) del INEI, cerca del 40% de los niños menores de 3 años presenta algún tipo de anemia. La anemia se define como una concentración de hemoglobina por debajo de los umbrales establecidos por la Organización Mundial de la Salud (OMS): menos de 11 g/dL en niños de 6 a 59 meses, ajustado por altitud. Puede clasificarse como leve (10.0–10.9 g/dL), moderada (7.0–9.9 g/dL) o grave (< 7.0 g/dL).

Las consecuencias de la anemia en la primera infancia son severas e irreversibles en algunos casos: afecta el desarrollo cognitivo, reduce la capacidad de aprendizaje, frena el crecimiento físico y debilita el sistema inmune. La anemia en los primeros años de vida tiene impactos que perduran hasta la adultez en términos de capital humano y productividad económica. Por ello, el Estado peruano ha implementado diversas intervenciones orientadas a reducirla, entre las cuales Qali Warma es una de las de mayor alcance poblacional.

### 1.3 Motivación del estudio: el problema de identificación causal

Evaluar si Qali Warma **causa** una reducción en la anemia es más difícil de lo que parece. Una comparación simple entre niños que reciben el programa y niños que no lo reciben estaría plagada de **sesgo de selección**: los niños que ya están inscritos en el programa pueden diferir de los que no están, en variables no observables como los hábitos alimenticios del hogar, el nivel de pobreza, la accesibilidad geográfica a los centros educativos, o el capital social de la familia. Una regresión ordinaria que no controle adecuadamente por estas diferencias entregará un estimador sesgado del efecto causal del programa.

La **Regresión Discontinua** es precisamente la herramienta diseñada para resolver este problema cuando la elegibilidad a un programa está determinada por una regla de corte sobre una variable continua. Al comparar niños que están justo por encima y justo por debajo del umbral de 36 meses, podemos asumir que ambos grupos son comparables en sus características no observables, ya que la asignación al tratamiento es, en ese margen, virtualmente aleatoria.

### 1.4 Pregunta de investigación

> **¿El acceso al programa Qali Warma reduce la probabilidad de anemia en niños de alrededor de 36 meses de edad?**

### 1.5 Vinculación con los trabajos previos del curso

Este trabajo se apoya en los conceptos y herramientas desarrollados a lo largo del curso. Del **Módulo 5** (Regresión con variables dependientes binarias), retomamos el uso de modelos de probabilidad lineal (MPL/OLS) y logit para analizar la anemia como variable binaria, así como la construcción de variables desde la ENDES del INEI. Del **Módulo 6** (Diseños Cuasi-Experimentales), aplicamos directamente la metodología de RD con regresión lineal local, la lógica de comparación en el umbral y las pruebas de validez del diseño, siguiendo la misma estructura del ejemplo visto en clase (IFH / curative). Del **Módulo 4** (Regresión Múltiple), retomamos el uso de errores robustos (HC3) y la inclusión de covariables como controles. Del **Módulo 3** (Regresión Simple), la base conceptual del modelo de regresión lineal y la interpretación de coeficientes.

---

## 2. Marco Teórico y Estrategia Empírica

### 2.1 Fuente de variación exógena (Requisito 1 del trabajo)

La **fuente de variación exógena** en este estudio es la **regla administrativa de elegibilidad de Qali Warma basada en la edad**: solo los niños de 36 meses o más pueden acceder a las raciones del programa a través de los centros de educación inicial. Esta regla es establecida por política pública y se aplica uniformemente, con independencia de las características individuales de cada niño o de su hogar.

La clave de la exogeneidad radica en que esta regla no fue diseñada pensando en los niveles de anemia de los niños: fue establecida en función de la estructura del sistema educativo peruano (la edad mínima para el nivel inicial). En consecuencia, la variación en la exposición al programa que se produce en torno al umbral de 36 meses no está correlacionada con características del niño o del hogar que también afecten la anemia. Esto es exactamente lo que necesitamos para inferencia causal.

Esto es análogo al **Índice de Focalización de Hogares (IFH)** visto en clase: así como el IFH determina de forma exógena quién recibe o no una transferencia social en función de su puntaje respecto a un umbral administrativo, en nuestro caso la **edad en meses** determina de forma exógena quién recibe o no la alimentación de Qali Warma en función del umbral de 36 meses.

### 2.2 Regla de asignación al tratamiento (Requisito 2 del trabajo)

La **variable de corte** (o *running variable*) es la **edad del niño en meses** ($X_i$). La regla de asignación al tratamiento es determinística y viene dada por:

$$D_i = \mathbf{1}[X_i \geq 36]$$

donde $D_i = 1$ indica que el niño es **elegible** para recibir Qali Warma, y $D_i = 0$ indica que **no es elegible**. El umbral de corte es $c = 36$ meses.

Esta regla divide a nuestra muestra en dos grupos:

- **Grupo control** ($D_i = 0$): niños de 33 a 35 meses, no elegibles para el programa.
- **Grupo tratado** ($D_i = 1$): niños de 36 a 39 meses, elegibles para el programa.

Una consideración importante es si el diseño es **sharp** o **fuzzy**. En un diseño **sharp**, la probabilidad de recibir el tratamiento salta de 0 a 1 exactamente en el umbral: todos los elegibles reciben el programa y ningún no-elegible lo recibe. En un diseño **fuzzy**, el salto es parcial: puede haber niños elegibles que no reciben el programa (por inasistencia o no inscripción) o niños no elegibles que lo reciben (por error administrativo o acceso a programas similares). El tipo de diseño se verifica empíricamente en el Gráfico 3 del análisis.

### 2.3 Estimador del efecto causal (Requisito 3 del trabajo)

El estimador de Regresión Discontinua identifica el efecto causal del programa en el umbral, formalmente definido como:

$$\hat{\tau}_{RD} = \lim_{x \to c^+} \mathbb{E}[Y_i | X_i = x] - \lim_{x \to c^-} \mathbb{E}[Y_i | X_i = x]$$

Este estimador es el **Efecto Local Promedio del Tratamiento (LATE)** para los niños que están exactamente en el umbral de 36 meses.

Para implementar este estimador, estimamos el siguiente modelo de **regresión lineal local** con pendientes distintas a cada lado del umbral:

$$Y_i = \alpha + \tau D_i + \beta_1 (X_i - c) + \beta_2 D_i \cdot (X_i - c) + \varepsilon_i$$

donde:

- $Y_i$ es la variable de resultado: indicador de anemia (1 = tiene anemia, 0 = no tiene anemia)
- $D_i$ es el indicador de elegibilidad: $D_i = \mathbf{1}[X_i \geq 36]$
- $X_i - c$ es la edad del niño centrada en el umbral (variable de corte centrada)
- $D_i \cdot (X_i - c)$ es la interacción entre elegibilidad y variable de corte, que permite que la pendiente sea distinta a cada lado
- $\tau$ es el **estimador de RD**: la diferencia en la probabilidad de anemia exactamente en el umbral entre los elegibles y los no elegibles, que interpretamos como el efecto causal del programa
- Los errores estándar se corrigen por heterocedasticidad usando el estimador robusto HC3

El **supuesto de identificación** es la **continuidad del potencial de resultados en el umbral**: en ausencia del programa, la probabilidad de tener anemia sería una función continua de la edad en el umbral. Este supuesto es plausible porque no existe ninguna razón biológica, económica ni social para que la anemia deba cambiar abruptamente al cumplir exactamente 36 meses. Lo que cambia en el umbral es únicamente la elegibilidad al programa.

### 2.4 Potenciales problemas con la estrategia (Requisito 4 del trabajo)

Identificamos cuatro amenazas principales a la validez del diseño:

**Amenaza 1 — Manipulación de la variable de corte:** Si los padres o instituciones manipulan la edad registrada de los niños para cruzar el umbral, el grupo de control y el grupo de tratado ya no serían comparables en el margen. Por ejemplo, si los padres con mayor motivación y capacidad de gestión adelantan el registro de sus hijos, los elegibles serían sistemáticamente distintos a los no elegibles. Esta amenaza se evalúa con el **test de densidad (McCrary)**: bajo H₀ de no manipulación, la densidad de la variable de corte debe ser continua en el umbral.

**Amenaza 2 — Discontinuidades en covariables pre-tratamiento:** Si variables que predicen la anemia y son determinadas antes del tratamiento (como el sexo, el peso al nacer o el nivel educativo de la madre) presentan discontinuidades en el umbral, el supuesto de continuidad se vería violado. Esto sugeriría que los grupos difieren en características no observables que también afectan la anemia. Se evalúa con **tests de placebo en covariables** (Gráfico 4).

**Amenaza 3 — Selección inadecuada del bandwidth:** La elección del ancho de banda $h$ implica un trade-off entre sesgo (menor si $h$ es pequeño) y varianza (menor si $h$ es grande). Si el bandwidth es demasiado amplio, la aproximación lineal puede ser inadecuada y el estimador se sesga. Lo evaluamos reportando estimaciones para distintos valores de $h$ (Gráfico 5 y Tabla 2).

**Amenaza 4 — Efectos de anticipación y derrame:** Los padres podrían cambiar sus comportamientos alimentarios antes de que el niño cumpla 36 meses (efecto de anticipación), lo cual contaminaría el contrafactual. Asimismo, si los niños no elegibles tienen acceso a otros programas de alimentación similares, el grupo control también podría estar "tratado", lo que atenuaría el estimador de RD.

---

## 3. Datos

### 3.1 Fuente de datos

La base de datos utilizada en este análisis fue proporcionada por el curso y está disponible en el repositorio GitHub del docente:

```
https://github.com/krlos20203563/inferencia_causal/raw/refs/heads/main/base_RD_qaliwarma_anemia.csv
```

Esta base contiene información de niños peruanos de entre 33 y 39 meses de edad, con variables sobre su estado de salud (anemia), su edad exacta en meses, y covariables sociodemográficas del niño y del hogar. La fuente subyacente corresponde a datos administrativos y/o encuestas de salud (como la ENDES del INEI), procesados para el diseño de este ejercicio.

### 3.2 Descripción de variables

La siguiente tabla describe las variables incluidas en el análisis:

| Variable | Tipo | Descripción | Rol en el análisis |
|----------|------|-------------|-------------------|
| `anemia` | Binaria (0/1) | Indicador de si el niño tiene anemia (cualquier nivel). Valor 1 = tiene anemia, 0 = no tiene anemia | **Variable de resultado** ($Y_i$) |
| `edad_meses` | Continua (33-39) | Edad del niño en meses al momento de la encuesta | **Variable de corte / running variable** ($X_i$) |
| `elegible` | Binaria (0/1) | Indicador de elegibilidad para Qali Warma. Valor 1 si `edad_meses` ≥ 36 | **Variable de tratamiento** ($D_i$) |
| `running` | Continua | Edad del niño centrada en el umbral: `running = edad_meses - 36` | Variable de corte centrada en 0 |
| `sexo` | Binaria (0/1) | Sexo del niño (1 = mujer, 0 = hombre) | **Covariable pre-tratamiento** |
| `peso` | Continua | Peso del niño en kg al momento de la encuesta | **Covariable pre-tratamiento** |
| `talla` | Continua | Talla del niño en cm | **Covariable pre-tratamiento** |
| `hemoglobina` | Continua | Nivel de hemoglobina en g/dL (ajustada por altitud) | Variable relacionada con el resultado |
| `edad_madre` | Continua | Edad de la madre en años | **Covariable pre-tratamiento** |
| `educ_madre` | Categórica | Nivel educativo de la madre (0=sin educación, 1=primaria, 2=secundaria, 3=superior) | **Covariable pre-tratamiento** |
| `rural` | Binaria (0/1) | Área de residencia (1 = rural, 0 = urbano) | **Covariable pre-tratamiento** |

**Notas importantes sobre las variables:**

- La **variable de resultado** (anemia) es una variable binaria que toma el valor 1 si el niño tiene algún nivel de anemia (leve, moderada o grave) y 0 si no. Al trabajar con una variable dependiente binaria y un estimador OLS (Modelo de Probabilidad Lineal), el coeficiente $\tau$ se interpreta como la diferencia en **puntos porcentuales** en la probabilidad de anemia. Seguimos el enfoque del Módulo 5 del curso, donde se trabajó con la anemia de niños de la ENDES con este mismo tipo de variable.
- La **variable de corte centrada** (`running`) facilita la interpretación: el umbral queda en 0, los valores negativos corresponden al grupo control y los positivos al grupo tratado.
- Las **covariables pre-tratamiento** son variables que no pueden ser afectadas por el programa porque son determinadas antes de la exposición (sexo, características de la madre) o son predictores relevantes de la anemia.

### 3.3 Estadísticas descriptivas

La Tabla 1 presenta las estadísticas descriptivas de las variables principales, diferenciando entre el grupo control (niños de 33 a 35 meses) y el grupo tratado (niños de 36 a 39 meses). Esta tabla tiene dos funciones: (1) describir la muestra de análisis y (2) verificar si los grupos son comparables en sus características pre-tratamiento, lo cual es una condición necesaria para la validez del diseño RD.

*(Ver Tabla 1 en el notebook)*

**Cómo leer la Tabla 1:** La columna "Diferencia de medias" muestra la diferencia entre el grupo tratado y el control para cada variable. Para las covariables pre-tratamiento (sexo, peso, edad de la madre, etc.), esta diferencia debería ser pequeña y estadísticamente no significativa. Para la variable de resultado (anemia), una diferencia negativa y significativa sería la primera indicación descriptiva de un posible efecto del programa, aunque no suficiente para establecer causalidad.

---

## 4. Análisis Gráfico

El análisis gráfico es un componente fundamental de cualquier estudio de Regresión Discontinua. Los gráficos permiten verificar visualmente los supuestos del diseño, detectar posibles problemas y comunicar los resultados de manera intuitiva antes de presentar las estimaciones formales.

### 4.1 Gráfico 1: Distribución de la variable de corte — Test visual de McCrary

![Gráfico 1: Distribución de la variable de corte](https://github.com/krlos20203563/TF_Causal_Inference/blob/main/grafico1_densidad_corte.png?raw=true)

**Qué muestra:** El panel A muestra el número de niños en cada mes de edad dentro de la ventana de análisis (33-39 meses). Los colores diferencian el grupo control (azul, < 36 meses) del grupo tratado (rojo, ≥ 36 meses). El panel B muestra la función de densidad estimada (KDE) a cada lado del umbral.

**Qué buscamos:** Para que el diseño RD sea válido, la distribución de la variable de corte debe ser **continua en el umbral**. Es decir, no debe haber una acumulación inusual de niños justo por encima o justo por debajo de los 36 meses. Si observáramos una "montaña" de observaciones justo después del corte, ello indicaría que padres o instituciones están manipulando la edad registrada para que el niño ingrese antes al programa.

**Interpretación:** Si la distribución es relativamente uniforme a ambos lados del umbral, sin saltos visibles en la densidad, el test sugiere ausencia de manipulación. Dado que la edad de los niños se verifica con documentos de identidad (DNI) en el sistema del MINSA y MIDIS, la manipulación directa de este dato es poco probable en el contexto peruano.

---

### 4.2 Gráfico 2: Gráfico RD principal — Prevalencia de anemia y umbral de eligibilidad

![Gráfico 2: RD principal de anemia](https://github.com/krlos20203563/TF_Causal_Inference/blob/main/grafico2_RD_principal.png?raw=true)

**Qué muestra:** Cada punto representa la proporción de niños con anemia para cada mes de edad. Las líneas muestran el ajuste de regresión lineal local a cada lado del umbral, con bandas de confianza al 95%. El panel A usa la ventana completa (33-39 meses) y el panel B una ventana más estrecha (34.5-37.5 meses) para ampliar el detalle alrededor del corte.

**Qué buscamos:** El estimador de RD es precisamente el salto vertical en las líneas de ajuste exactamente en el umbral de 36 meses. Si Qali Warma **reduce** la anemia, esperaríamos un salto hacia **abajo** en la probabilidad de anemia al cruzar el umbral: los niños recién elegibles (36 meses) deberían tener menor anemia que la extrapolación predice para los no elegibles (35 meses). Si no hay salto, no encontramos evidencia de efecto.

**Interpretación:** El gráfico también permite verificar si la tendencia a cada lado del umbral es aproximadamente lineal (lo que justifica el uso de un polinomio de orden 1), o si se requiere un polinomio de mayor orden para capturar la curvatura de la relación entre edad y anemia.

---

### 4.3 Gráfico 3: Probabilidad de tratamiento — ¿Diseño Sharp o Fuzzy?

![Gráfico 3: Sharp vs Fuzzy](https://github.com/krlos20203563/TF_Causal_Inference/blob/main/grafico3_sharp_fuzzy.png?raw=true)

**Qué muestra:** Este gráfico muestra cómo varía la probabilidad de recibir el tratamiento (ser beneficiario de Qali Warma) en función de la edad del niño.

**Qué buscamos:** En un **diseño Sharp**, la probabilidad de tratamiento salta de exactamente 0 a exactamente 1 en el umbral: todos los elegibles reciben el programa y ningún no-elegible lo recibe. En este caso, la elegibilidad es un predictor perfecto del tratamiento real. En un **diseño Fuzzy**, el salto es parcial, lo que implica cumplimiento imperfecto. En ese caso, la elegibilidad debe usarse como **instrumento** para el tratamiento real (diseño IV/2SLS), y el estimador identificado corresponde al LATE solo para los "compliers" (aquellos que efectivamente cambian su estado de tratamiento al cruzar el umbral).

**Implicancia metodológica:** Si el diseño es Sharp, el estimador de RD estándar identifica directamente el ATT en el umbral. Si es Fuzzy, el estimador de Reduced Form (regresando anemia sobre elegibilidad) identifica el Efecto de la Intención de Tratar (ITT), y para obtener el LATE se debe dividir por el "first stage" (el salto en la probabilidad de tratamiento).

---

### 4.4 Gráfico 4: Tests de placebo en covariables pre-tratamiento

![Gráfico 4: Placebo en covariables]

**Qué muestra:** Para cada covariable pre-tratamiento disponible (sexo, peso, edad de la madre, etc.), el gráfico muestra si existe una discontinuidad en el umbral de 36 meses, siguiendo la misma metodología del gráfico RD principal.

**Qué buscamos:** Las covariables pre-tratamiento no deben presentar discontinuidades estadísticamente significativas en el umbral. Si el sexo del niño, por ejemplo, presentara un salto significativo en 36 meses, ello indicaría que los grupos difieren en características que determinan la anemia independientemente del programa, lo que violaría el supuesto de continuidad.

**Interpretación:** Para cada covariable, reportamos el coeficiente del estimador RD y su p-valor. Un coeficiente no significativo (p > 0.10) apoya la validez del diseño. Este test es el análogo a la verificación del "balance de características" en un experimento aleatorio.

---

### 4.5 Gráfico 5: Sensibilidad al bandwidth

![Gráfico 5: Sensibilidad al bandwidth](https://github.com/krlos20203563/TF_Causal_Inference/blob/main/grafico5_sensibilidad_bw.png?raw=true)

**Qué muestra:** El panel A muestra el estimador RD y su intervalo de confianza al 95% para distintos valores del bandwidth, desde 0.5 hasta 3 meses. El panel B muestra el número de observaciones disponibles en cada ventana.

**Qué buscamos:** Un estimador **robusto** no debería cambiar de manera sistemática y drástica al variar el bandwidth. Si el estimador es consistentemente negativo (o positivo) y significativo para un rango amplio de bandwidths, eso refuerza la credibilidad del resultado. Si el signo o la significancia varía mucho, el resultado es frágil y debe interpretarse con cautela.

**Interpretación:** El trade-off fundamental es que bandwidths pequeños son más creíbles (la continuidad del potencial de resultados es más plausible para niños muy cercanos al umbral) pero entregan estimadores más imprecisos (menos observaciones). Bandwidths grandes incluyen más observaciones pero pueden introducir sesgo si la función de regresión no es lineal.

---

### 4.6 Gráfico 6: Test de placebo en el umbral (cortes falsos)

![Gráfico 6: Cortes falsos](https://github.com/krlos20203563/TF_Causal_Inference/blob/main/grafico6_placebo_corte.png?raw=true)

**Qué muestra:** Para cada edad posible en la ventana de análisis (de 33.5 a 38.5 meses), estimamos el modelo RD como si ese fuera el umbral real, y graficamos el coeficiente estimado con su intervalo de confianza al 95%.

**Qué buscamos:** Solo en el umbral real (c = 36 meses) debería existir un estimador estadísticamente significativo. Si encontramos discontinuidades significativas en otros puntos de edad, ello sugiere que hay factores distintos al programa causando los saltos observados.

**Interpretación:** Si el estimador es significativo únicamente en el corte real y no en los cortes falsos, ello constituye evidencia sólida de que el salto observado en 36 meses se debe efectivamente al programa y no a factores confundidores.

---

## 5. Resultados

### 5.1 Tabla de resultados principales

La Tabla 2 presenta las estimaciones del efecto causal de Qali Warma sobre la anemia en cuatro especificaciones distintas. Las diferencias entre especificaciones permiten evaluar la robustez de los resultados.

*(Ver Tabla 2 en el notebook)*

**Cómo leer la Tabla 2:**

- **Especificación (1):** Modelo base, sin covariables, usando el bandwidth óptimo. El coeficiente de "Elegible" ($\hat{\tau}_{RD}$) es el estimador puro de la discontinuidad en el umbral.
- **Especificación (2):** Mismo bandwidth, pero controlando por covariables disponibles (sexo, peso, características de la madre, área de residencia). Incluir covariables no cambia el estimador puntual si el diseño es válido, pero reduce los errores estándar.
- **Especificación (3):** Bandwidth más estrecho (60% del óptimo). Al reducir la ventana, los grupos son más similares pero hay menos observaciones.
- **Especificación (4):** Bandwidth más amplio (150% del óptimo). Mayor precisión estadística, pero mayor riesgo de sesgo si la relación no es perfectamente lineal.

### 5.2 Interpretación del estimador RD

El **coeficiente $\hat{\tau}_{RD}$** de la Especificación (1) debe leerse de la siguiente manera: representa el cambio en la probabilidad de tener anemia (en puntos porcentuales) que se produce exactamente en el umbral de 36 meses, atribuible a la elegibilidad para Qali Warma. 

- Si $\hat{\tau}_{RD} < 0$ y es estadísticamente significativo: el programa **reduce** la probabilidad de anemia. Por ejemplo, un coeficiente de -0.05 indicaría que ser elegible para el programa reduce la probabilidad de anemia en 5 puntos porcentuales en el umbral.
- Si $\hat{\tau}_{RD}$ no es estadísticamente significativo: no encontramos evidencia de que el programa tenga efecto sobre la anemia en el umbral. Esto puede deberse a que el efecto es genuinamente nulo, o a que la muestra es insuficiente para detectarlo.

**Magnitud del efecto:** Para contextualizar el coeficiente, es útil expresarlo como porcentaje de la prevalencia de anemia en el grupo control. Si la prevalencia de anemia en niños de 33-35 meses es, por ejemplo, del 45%, un coeficiente de -0.05 representaría una reducción relativa de aproximadamente 11%.

**Validez local del estimador:** Es fundamental recordar que el estimador RD solo tiene validez causal para los niños en el margen del umbral (alrededor de 36 meses). No podemos generalizar el resultado a niños de 12 meses ni a niños de 5 años. Este es el costo de la credibilidad causal del diseño.

---

## 6. Pruebas de Robustez

### 6.1 Test de densidad (McCrary)

El test de McCrary evalúa si la distribución de la variable de corte presenta una discontinuidad en el umbral, lo que indicaría manipulación. Bajo la hipótesis nula de no manipulación, la densidad de la edad debe ser continua en c = 36 meses. Los resultados del test visual (Gráfico 1) y la comparación del número de observaciones a cada lado del umbral nos permiten evaluar esta amenaza. Si no se rechaza H₀, la manipulación no representa una amenaza significativa.

### 6.2 Tests de placebo en covariables

Como se discutió en la sección del Gráfico 4, los tests de placebo en covariables pre-tratamiento verifican si el umbral coincide con algún cambio en las características de los niños. La Tabla de resultados del placebo (dentro del notebook) reporta el coeficiente RD y el p-valor para cada covariable. El hallazgo deseable es que **ninguna covariable sea estadísticamente significativa** en el umbral.

### 6.3 Sensibilidad al bandwidth

La Tabla 2 y el Gráfico 5 presentan estimaciones para distintos valores del bandwidth. La consistencia del estimador a lo largo de estos distintos anchos de ventana es una de las pruebas de robustez más importantes en RD. Si el signo y la magnitud del coeficiente se mantienen relativamente estables para bandwidths entre 1 y 3 meses, el resultado es robusto.

### 6.4 Polinomio de orden 2

La Tabla 3 (en el notebook) compara el estimador bajo un polinomio local de orden 1 (lineal) y orden 2 (cuadrático). La elección del orden del polinomio afecta el estimador: un polinomio de mayor orden es más flexible pero tiene mayor varianza. Si ambas especificaciones entregan coeficientes similares, ello sugiere que la relación entre edad y anemia es aproximadamente lineal en la ventana de análisis.

### 6.5 Test de cortes falsos

El Gráfico 6 muestra que el salto en la variable de resultado debería ocurrir **únicamente** en el umbral real de 36 meses y no en otros puntos de edad. Si el estimador es significativo solo en c = 36 y no en c = 34 ni c = 38, ello constituye evidencia adicional de que el salto observado se debe al programa y no a factores confundidores.

---

## 7. Discusión de Amenazas y Limitaciones

### 7.1 Evaluación de las amenazas identificadas

Retomamos las cuatro amenazas identificadas en la sección 2.4 y evaluamos su severidad a la luz de los resultados:

**Amenaza 1 (Manipulación):** El test de densidad (Gráfico 1) nos permite evaluar si hay acumulación de observaciones en el umbral. En el contexto peruano, la edad de los niños está registrada con base en el DNI, lo que hace que la manipulación directa sea difícil. Sin embargo, podrían existir errores de medición o rezagos en la actualización de los registros. Si el test visual no muestra irregularidades, concluimos que esta amenaza es menor.

**Amenaza 2 (Covariables):** Los tests de placebo en covariables (Gráfico 4 y tabla asociada) nos indican si el supuesto de continuidad es razonable. Si las covariables pre-tratamiento no presentan discontinuidades significativas, el diseño es internamente válido.

**Amenaza 3 (Bandwidth):** El análisis de sensibilidad (Gráfico 5) nos muestra si el estimador es frágil a la elección del bandwidth. La selección del bandwidth óptimo sigue un criterio inspirado en el método de Calonico, Cattaneo y Titiunik (CCT), que minimiza el error cuadrático medio del estimador de RD considerando el trade-off entre sesgo y varianza.

**Amenaza 4 (Anticipación y derrame):** Esta amenaza es más difícil de evaluar directamente con los datos disponibles. Los efectos de anticipación implicarían que los padres de niños de 35 meses ya modifican su dieta en preparación para el ingreso al programa, lo que contaminaría el grupo control. Una forma indirecta de evaluar esto es analizar si el estimador se vuelve más negativo al reducir el bandwidth (lo que excluiría a los niños más alejados del umbral).

### 7.2 Limitaciones adicionales

**Validez externa:** El estimador de RD es válido localmente en el umbral. Los niños de exactamente 36 meses pueden ser sistemáticamente distintos en sus condiciones de salud o nutrición a los niños de 24 meses o de 48 meses. Por ello, no podemos extrapolar el efecto encontrado a toda la población beneficiaria del programa.

**Heterogeneidad del efecto:** El programa puede tener efectos distintos según el área geográfica (urban/rural), el nivel de pobreza del hogar, o la disponibilidad de infraestructura de los centros educativos. El estimador global de RD captura un promedio ponderado de estos efectos heterogéneos.

**Calidad del tratamiento:** Qali Warma opera a través de una cadena logística compleja (comités de alimentación, proveedores locales). La calidad y consistencia de la ración entregada puede variar significativamente entre regiones, lo que puede atenuar el efecto promedio estimado.

**Duración de la exposición:** Los niños de exactamente 36 meses apenas acaban de iniciar su exposición al programa. Es posible que los efectos del programa sean mayores para niños con mayor tiempo de exposición. El estimador RD captura el efecto en el inicio del tratamiento, no el efecto acumulado.

---

## 8. Conclusiones

Este trabajo aplica la metodología de Regresión Discontinua para estimar el efecto causal del programa de alimentación escolar Qali Warma sobre la prevalencia de anemia en niños de 33 a 39 meses, explotando el umbral de elegibilidad de 36 meses como fuente de variación exógena.

**Sobre el diseño:** La estrategia de RD es válida en este contexto porque la elegibilidad para Qali Warma está determinada por una regla administrativa basada en la edad, que es exógena a las características individuales que afectan la anemia. Los tests de validación —densidad de la variable de corte, covariables pre-tratamiento, cortes falsos y sensibilidad al bandwidth— evalúan la fortaleza de este diseño.

**Sobre los resultados:** El estimador de RD identifica el Efecto Local Promedio del Tratamiento (LATE) para los niños en el margen de 36 meses. El tamaño, signo y significancia estadística del estimador dependen de los datos reales de la base. Si el coeficiente es negativo y significativo, ello constituiría evidencia causal de que Qali Warma reduce la anemia en el umbral de elegibilidad. Si no es significativo, no podemos concluir que el programa tenga efecto detectable en este margen.

**Sobre las implicancias de política:** Si se detecta un efecto positivo y significativo del programa en el umbral, esto refuerza la pertinencia de mantener y escalar Qali Warma como intervención de nutrición infantil. Sin embargo, la validez del resultado es local: aplica a niños alrededor de los 36 meses en el margen de eligibilidad. Extender las conclusiones al conjunto de la población beneficiaria requiere argumentos adicionales. Asimismo, el hecho de que el umbral de 36 meses sea exógeno no implica que sea óptimo desde el punto de vista nutricional: un análisis costo-beneficio más amplio debería evaluar si bajar la edad de elegibilidad tendría mayores beneficios.

**Sobre la metodología:** Este trabajo integra los conceptos fundamentales desarrollados a lo largo del curso: la definición rigurosa del problema de inferencia causal, la identificación de fuentes de variación exógena, la implementación de regresiones con variables dependientes binarias, el uso de errores estándar robustos, y la aplicación de diseños cuasi-experimentales. La Regresión Discontinua es una herramienta poderosa precisamente porque su credibilidad no descansa en supuestos inobservables sobre la equivalencia de grupos, sino en una regla de asignación transparente que puede verificarse empíricamente.

---

## Referencias

- Calonico, S., Cattaneo, M. D., & Titiunik, R. (2014). Robust Nonparametric Confidence Intervals for Regression-Discontinuity Designs. *Econometrica*, 82(6), 2295–2326.
- Cattaneo, M. D., Idrobo, N., & Titiunik, R. (2018). *A Practical Introduction to Regression Discontinuity Designs: Foundations*. Cambridge University Press.
- Hahn, J., Todd, P., & Van der Klaauw, W. (2001). Identification and Estimation of Treatment Effects with a Regression-Discontinuity Design. *Review of Economic Studies*, 68(1), 201–209.
- McCrary, J. (2008). Manipulation of the Running Variable in the Regression Discontinuity Design: A Density Test. *Journal of Econometrics*, 142(2), 698–714.
- MIDIS (2023). *Programa Nacional de Alimentación Escolar Qali Warma: Marco normativo y de operación*. Lima: Ministerio de Desarrollo e Inclusión Social del Perú.
- INEI (2023). *Encuesta Demográfica y de Salud Familiar (ENDES) 2023*. Lima: Instituto Nacional de Estadística e Informática del Perú.
- Q-LAB (2026). *Práctica Módulo 5: Regresión con Variables Dependientes Binarias — Análisis de Anemia con ENDES*. Material del curso Fundamentos de Inferencia Causal, Escuela de Verano.
- Q-LAB (2026). *Práctica Módulo 6: Diseños Cuasi-Experimentales — Variables Instrumentales, Diferencias en Diferencias y Regresión Discontinua*. Material del curso Fundamentos de Inferencia Causal, Escuela de Verano.

---

*Nota: Este documento es autocontenido. Para ver el código fuente y la ejecución completa del análisis, consultar el notebook `analisis_RD_qaliwarma.ipynb` en el mismo directorio. Los gráficos referenciados se encuentran en la subcarpeta `figuras/`.*
