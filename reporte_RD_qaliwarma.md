---
output:
  html_document: default
  pdf_document: default
  word_document: default
---
# Impacto del Programa Qali Warma sobre la Anemia Infantil: Un Análisis de Regresión Discontinua

**Trabajo Final — Fundamentos de Inferencia Causal**  
**Integrantes**:
- Julia Massa
- Carlos
- Ibeeth Medina Silva (20214029)
---

## Resumen

Este trabajo de Inferencia Causal busca estimar el efecto del programa de alimentación escolar **Qali Warma** sobre la anemia en niños de 33 a 39 meses de edad. Para ello se utiliza una estrategia de Regresión Discontinua (RD).

La idea se basa en la regla de elegibilidad del programa: los niños que tienen 36 meses o más pueden acceder al programa en centros de educación inicial públicos, mientras que los menores de 36 meses no. Esta diferencia funciona como un punto de corte que permite comparar a niños que son muy similares entre sí, pero donde unos pueden recibir el programa y otros no.

De esta manera, el método compara la probabilidad de anemia entre los niños que están justo por encima y justo por debajo de los 36 meses, lo que permite aproximar el efecto del programa. Este efecto se conoce como Efecto Local Promedio del Tratamiento (LATE), ya que se estima solo alrededor de ese punto de corte.

Además, se realizan algunas pruebas de robustez, para verificar que los grupos comparados sean realmente similares y que los resultados sean confiables.

---

## 1. Introducción

### 1.1 El programa Qali Warma

**Qali Warma** (en quechua: "niño vigoroso") es el programa de alimentación escolar del Estado peruano, administrado por el Ministerio de Desarrollo e Inclusión Social (MIDIS). Su propósito es garantizar el servicio de alimentación a los niños y niñas que asisten a instituciones educativas públicas a lo largo de todo el año escolar, con el fin de mejorar su atención, asistencia y hábitos alimenticios (CITA).

El programa opera en dos niveles: inicial y primaria. En el nivel inicial, la elegibilidad comienza a los **36 meses de edad** (3 años cumplidos), que es la edad mínima para ingresar a los Centros de Educación Inicial (CEI) o Programas No Escolarizados de Educación Inicial (PRONOEI) que reciben las raciones del programa. Esta regla de corte por edad es la base de nuestra estrategia empírica para el análisis.

El programa entrega a los niños elegibles una ración alimentaria diaria durante los días del calendario escolar. Las raciones están diseñadas para cubrir entre el 20% y el 30% de los requerimientos nutricionales diarios del niño, con especial atención al aporte de hierro, zinc y otros micronutrientes clave para prevenir la anemia. En ese sentido, se espera que el programa reduzca la prevalencia de anemia en los niños beneficiarios del mismo.

### 1.2 La anemia infantil en el Perú como problema de política pública

La anemia infantil es uno de los principales problemas de salud pública del Perú. Según la Encuesta Demográfica y de Salud Familiar (ENDES) del INEI, cerca del 40% de los niños menores de 3 años presenta algún tipo de anemia. La anemia se define como una concentración de hemoglobina por debajo de los umbrales establecidos por la Organización Mundial de la Salud (OMS): menos de 11 g/dL en niños de 6 a 59 meses, ajustado por altitud. Puede clasificarse como leve (10.0–10.9 g/dL), moderada (7.0–9.9 g/dL) o grave (< 7.0 g/dL).

Las consecuencias de la anemia en la primera infancia son severas e irreversibles en algunos casos, puesto que entre sus efectos se encuentran: la afectación al desarrollo cognitivo, la reducción de la capacidad de aprendizaje, un tardío en el crecimiento físico y la deilitación del sistema inmune. La anemia en los primeros años de vida tiene impactos que perduran hasta la adultez en términos de capital humano y productividad económica. Por ello, el Estado peruano ha implementado diversas intervenciones orientadas a reducirla, entre las cuales Qali Warma es una de las más conocidas por su mayor alcance poblacional.

### 1.3 Motivación del estudio: el problema de identificación causal

Evaluar si Qali Warma **causa** una reducción en la anemia es más difícil de lo que parece. Según Vargas (2022), este programa garantizó la alimentación de alrededor de 4 millones de niños en edades escolares. En ese sentido, este programa aparenta ser un elemento clave en la lucha contra la desnutrición y la anemia en el Perú. Sin embargo, tal como sostiene Francke (2021), aparentemente no ha ejercido gran influencia a nivel nacional en los últimos años. 

En ese sentido, una comparación simple entre niños que reciben el programa y niños que no lo reciben habría bastante sesgo de selección puesto que los niños que ya están inscritos en el programa pueden diferir de los que no están, en variables no observables como los hábitos alimenticios del hogar, el nivel de pobreza, la accesibilidad geográfica a los centros educativos, o el capital social de la familia.

La **Regresión Discontinua** es precisamente la herramienta diseñada para resolver este problema cuando la elegibilidad a un programa está determinada por una regla de corte sobre una variable continua. Al comparar niños que están justo por encima y justo por debajo del umbral de 36 meses, podemos asumir que ambos grupos son comparables en sus características no observables, ya que la asignación al tratamiento es, en ese margen, aparentemente aleatoria.

### 1.4 Pregunta de investigación

> **¿El acceso al programa Qali Warma reduce la probabilidad de anemia en niños de alrededor de 36 meses de edad?**


## 2. Marco Teórico y Estrategia Empírica

### 2.1 Fuente de variación exógena (Requisito 1 del trabajo)

La **fuente de variación exógena** en este estudio es la **regla administrativa de elegibilidad de Qali Warma basada en la edad**: solo los niños de 36 meses o más pueden acceder a las raciones del programa a través de los centros de educación inicial. Esta regla es establecida por política pública y se aplica uniformemente, con independencia de las características individuales de cada niño o de su hogar.

La **exogeneidad** se basa en que esta regla no se creó pensando en los niveles de anemia de los niños, sino en cómo está organizado el sistema educativo peruano (la edad mínima para entrar al nivel inicial). Por eso, el hecho de que algunos niños puedan acceder al programa justo después de cumplir 36 meses no depende de características del niño o de su hogar que también influyan en la anemia. Esto es lo que permite usar esta situación para hacer **inferencia causal**.


### 2.2 Variable de corte

La **variable de corte** es la **edad del niño en meses**.

Nuestra muestra en dos grupos:

- Grupo control : niños de 33 a 35 meses, no elegibles para el programa.
- Grupo tratado : niños de 36 a 39 meses, elegibles para el programa.


### 2.3 Estimador del efecto causal

El estimador de Regresión Discontinua identifica el efecto causal del programa en el umbral, formalmente definido como:


Este estimador es el **Efecto Local Promedio del Tratamiento (LATE)** para los niños que están exactamente en el umbral de 36 meses

El **supuesto de identificación** es la **continuidad del potencial de resultados en el umbral**. Esto significa que, si el programa no existiera, la probabilidad de que un niño tenga anemia debería cambiar de forma gradual con la edad, incluso alrededor de los 36 meses.

Este supuesto es razonable porque no hay una razón biológica, económica o social para que la anemia cambie de forma repentina justo cuando un niño cumple exactamente 36 meses. En realidad, lo único que cambia en ese punto es la posibilidad de acceder al programa.

### 2.4 Potenciales problemas con la estrategia 

Identificamos cuatro amenazas principales a la validez del diseño:

**Amenaza 1 : Manipulación de la variable de corte:**
Si los padres o instituciones modificaran la edad registrada de los niños para que pasen el umbral, el grupo de control y el grupo tratado dejarían de ser comparables. Por ejemplo, algunos padres podrían adelantar el registro de sus hijos para que accedan antes al programa. Para evaluar esto se usa el **test de densidad**, si no hay manipulación, la densidad de la edad debería verse continua alrededor del umbral.

**Amenaza 2 : Discontinuidades en covariables pre-tratamiento:**
Si variables que influyen en la anemia y que se determinan antes del programa (como el sexo del niño, el peso al nacer o la educación de la madre) cambian bruscamente en el umbral, entonces el supuesto de continuidad no se cumpliría. Esto indicaría que los grupos no son realmente comparables. Para revisarlo se usan **tests de placebo en covariables** (Gráfico 4).

**Amenaza 3 : Efectos de anticipación y derrame:**
Los padres podrían cambiar la alimentación del niño antes de que cumpla 36 meses porque saben que pronto recibirá el programa (efecto de anticipación). También podría ocurrir que algunos niños no elegibles accedan a otros programas de alimentación similares. En ambos casos, el grupo de control dejaría de ser un verdadero contrafactual y el efecto estimado del programa podría verse reducido.


---

## 3. Datos

### 3.1 Fuente de datos

La base de datos utilizada se encuentra en el siguiente link: 

[https://github.com/krlos20203563/inferencia_causal/raw/refs/heads/main/base_RD_qaliwarma_anemia.csv](https://raw.githubusercontent.com/krlos20203563/inferencia_causal/refs/heads/main/base_RD_qaliwarma_anemia.csv)

Esta base contiene información de niños peruanos de entre 33 y 39 meses de edad, con variables sobre su estado de salud (anemia), su edad exacta en meses, y covariables sociodemográficas del niño y del hogar.

### 3.2 Descripción de variables

La siguiente tabla describe las variables incluidas en el análisis:

Aquí la tienes en **Markdown**, para que la pegues directamente en GitHub y te salga la tabla:


| Variable | Tipo | Descripción | Rol en el análisis |
|----------|------|-------------|-------------------|
| `anemia` | Binaria (0/1) | Indica si el niño tiene anemia. 1 = tiene anemia, 0 = no tiene anemia | **Variable de resultado** ($Y_i$) |
| `edad_meses` | Continua (33–39) | Edad del niño en meses al momento de la encuesta | **Variable de corte / running variable** ($X_i$) |
| `elegible` | Binaria (0/1) | Indica si el niño puede acceder a Qali Warma. 1 si `edad_meses` ≥ 36 | **Variable de tratamiento** ($D_i$) |
| `running` | Continua | Edad del niño centrada en el umbral: `edad_meses - 36` | Variable de corte centrada en 0 |
| `sexo` | Binaria (0/1) | Sexo del niño (1 = mujer, 0 = hombre) | **Covariable pre-tratamiento** |
| `peso` | Continua | Peso del niño en kg | **Covariable pre-tratamiento** |
| `talla` | Continua | Talla del niño en cm | **Covariable pre-tratamiento** |
| `hemoglobina` | Continua | Nivel de hemoglobina en g/dL (ajustado por altitud) | Variable relacionada con el resultado |
| `edad_madre` | Continua | Edad de la madre en años | **Covariable pre-tratamiento** |
| `educ_madre` | Categórica | Nivel educativo de la madre (0=sin educación, 1=primaria, 2=secundaria, 3=superior) | **Covariable pre-tratamiento** |
| `rural` | Binaria (0/1) | Lugar de residencia (1 = rural, 0 = urbano) | **Covariable pre-tratamiento** |


**Notas importantes sobre las variables:**

Para este trabajo, nuestra variable de resultado es la anemia. Básicamente, la armamos como una variable dicotómica: le pusimos 1 si el niño presenta cualquier grado de anemia (ya sea leve, moderada o fuerte) y 0 si no tiene nada. Como estamos usando un Modelo de Probabilidad Lineal (OLS) con una dependiente binaria, el coeficiente $\tau$ se lee directamente como el cambio en puntos porcentuales de la probabilidad de estar anémico. Estamos aplicando lo mismo que vimos en el Módulo 5 del curso, donde analizamos los datos de la ENDES de la misma forma.En cuanto a la variable de corte (running variable), decidimos centrarla para que todo sea más fácil de interpretar. Al dejar el umbral en 0, sabemos de una que los valores negativos son el grupo de control y los positivos son los que recibieron el tratamiento.Finalmente, incluimos varias covariables pre-tratamiento (como el sexo del niño o factores de la madre). Estas variables son clave porque, al ser previas al programa, no se ven afectadas por este, pero sí nos ayudan a explicar mejor por qué un niño podría tener anemia.

### 3.3 Estadísticas descriptivas

La Tabla 1 presenta las estadísticas descriptivas de las variables principales, diferenciando entre el grupo control (niños de 33 a 35 meses) y el grupo tratado (niños de 36 a 39 meses). Esta tabla tiene dos funciones: (1) describir la muestra de análisis y (2) verificar si los grupos son comparables en sus características pre-tratamiento, lo cual es una condición necesaria para la validez del diseño RD.

*(Ver Tabla 1 en el notebook)*

Para entender la Tabla 1, hay que fijarse principalmente en la columna de "Diferencia de medias", que es donde comparamos al grupo tratado con el de control.

En las covariables (sexo, peso, edad de la madre, etc), lo ideal es que estas diferencias sean mínimas y que no salgan estadísticamente significativas. Eso nos daría una buena señal de que ambos grupos son comparables desde el inicio.

En la variable de resultado (anemia), lo que buscamos es una diferencia negativa y que sí sea significativa. Si sale así, sería nuestro primer "indicador" de que el programa podría estar funcionando, aunque claro, por ahora es solo descriptivo y no basta para asegurar que haya causalidad.

---

## 4. Análisis Gráfico

El análisis gráfico es un componente fundamental de cualquier estudio de Regresión Discontinua. Los gráficos permiten verificar visualmente los supuestos del diseño, detectar posibles problemas y comunicar los resultados de manera intuitiva antes de presentar las estimaciones formales.

### 4.1 Gráfico 1: Distribución de la variable de corte — Test visual de McCrary

![Gráfico 1: Distribución de la variable de corte](https://github.com/krlos20203563/TF_Causal_Inference/blob/main/grafico1_densidad_corte.png?raw=true)

En el Panel A, simplemente graficamos cuántos niños hay por cada mes de edad, enfocándonos en el rango de 33 a 39 meses. Separamos por colores para que se note la diferencia: en azul están los de control (menores de 36 meses) y en rojo los que ya entran al grupo tratado (de 36 meses para arriba). Por otro lado, el Panel B nos muestra la curva de densidad (KDE) a cada lado del corte.

¿Qué es lo que buscamos? Para que nuestra regresión discontinua sea válida, la distribución tiene que ser continua en el umbral. Es decir, no debería haber un "amontonamiento" raro de niños justo antes o justo después de los 36 meses. Si viéramos una "montaña" de datos apenas pasa el corte, sospecharíamos que alguien (ya sean los papás o la gente del programa) está manipulando la edad para que los niños califiquen antes de tiempo.

Interpretación: Como la distribución se ve bastante uniforme y no hay saltos extraños en la densidad, el test nos indica que no hay manipulación. Además, tiene sentido porque en Perú la edad se cruza con el DNI en los sistemas del MINSA y MIDIS, así que es bien difícil que alguien pueda "ajustar" la edad a su antojo.

---

### 4.2 Gráfico 2: Gráfico RD principal — Prevalencia de anemia y umbral de eligibilidad

![Gráfico 2: RD principal de anemia](https://github.com/krlos20203563/TF_Causal_Inference/blob/main/grafico2_RD_principal.png?raw=true)

Cada punto es el promedio de niños con anemia para cada mes de edad. Las líneas que atraviesan los puntos son el ajuste de la regresión a cada lado del corte, y las sombras que las acompañan son las bandas de confianza al 95%. Hicimos dos versiones: el Panel A muestra todo el rango (de 33 a 39 meses), y en el Panel B "hicimos zoom" en una ventana más pequeña (34.5 a 37.5 meses) para ver mejor qué pasa justo en el límite.

¿Qué es lo que buscamos? Básicamente, lo que nos importa es el salto vertical de las líneas justo cuando llegamos a los 36 meses. Si el programa Qali Warma de verdad funciona para reducir la anemia, lo lógico sería ver un salto hacia abajo: los niños que acaban de entrar al programa (36 meses) deberían tener menos anemia de lo que se esperaría según la tendencia de los que aún no califican. Si las líneas se ven continuas y no hay salto, pues no habría evidencia de que el programa esté haciendo efecto.

Interpretación: Aparte del salto, el gráfico nos sirve para chequear si la relación entre la edad y la anemia es más o menos una línea recta. Si se ve lineal, nos quedamos tranquilos usando un polinomio de orden 1 (una recta); pero si vemos que hace mucha curva, tendríamos que usar un modelo más complejo para que no se nos escape nada.
---

### 4.3 Gráfico 3: Probabilidad de tratamiento — ¿Diseño Sharp o Fuzzy?

![Gráfico 3: Sharp vs Fuzzy](https://github.com/krlos20203563/TF_Causal_Inference/blob/main/grafico3_sharp_fuzzy.png?raw=true)

Este gráfico muestra cómo varía la probabilidad de recibir el tratamiento (ser beneficiario de Qali Warma) en función de la edad del niño.

En un **diseño Sharp**, la probabilidad de tratamiento salta de exactamente 0 a exactamente 1 en el umbral: todos los elegibles reciben el programa y ningún no-elegible lo recibe. En este caso, la elegibilidad es un predictor perfecto del tratamiento real. En un **diseño Fuzzy**, el salto es parcial, lo que implica cumplimiento imperfecto. En ese caso, la elegibilidad debe usarse como **instrumento** para el tratamiento real (diseño IV/2SLS), y el estimador identificado corresponde al LATE solo para los "compliers" (aquellos que efectivamente cambian su estado de tratamiento al cruzar el umbral).

Si el diseño es Sharp, el estimador de RD estándar identifica directamente el ATT en el umbral. Si es Fuzzy, el estimador de Reduced Form (regresando anemia sobre elegibilidad) identifica el Efecto de la Intención de Tratar (ITT), y para obtener el LATE se debe dividir por el "first stage" (el salto en la probabilidad de tratamiento).

---

### 4.4 Gráfico 4: Tests de placebo en covariables pre-tratamiento

![Gráfico 4: Placebo en covariables]
Para cada covariable pre-tratamiento disponible (sexo, peso, edad de la madre, etc.), el gráfico muestra si existe una discontinuidad en el umbral de 36 meses, siguiendo la misma metodología del gráfico RD principal.

**¿Qué buscamos?:** Las covariables pre-tratamiento no deben presentar discontinuidades estadísticamente significativas en el umbral. Si el sexo del niño, por ejemplo, presentara un salto significativo en 36 meses, ello indicaría que los grupos difieren en características que determinan la anemia independientemente del programa, lo que violaría el supuesto de continuidad.

**Interpretación:** Para cada covariable, reportamos el coeficiente del estimador RD y su p-valor. Un coeficiente no significativo (p > 0.10) apoya la validez del diseño. Este test es el análogo a la verificación del "balance de características" en un experimento aleatorio.

---

### 4.5 Gráfico 5: Sensibilidad al bandwidth

![Gráfico 5: Sensibilidad al bandwidth](https://github.com/krlos20203563/TF_Causal_Inference/blob/main/grafico5_sensibilidad_bw.png?raw=true)
En estos dos paneles estamos probando qué tan sensible es nuestro resultado a la cantidad de datos que incluimos. El Panel A muestra cómo se mueve el estimador (y su intervalo de confianza al 95%) cuando cambiamos el "ancho de banda" o bandwidth, probando rangos desde 0.5 hasta 3 meses. El Panel B, por su parte, nos dice cuántos niños entran en el análisis en cada una de esas ventanas.

¿Qué buscamos con esto? queremos ver si nuestro resultado es robusto. Si el efecto que encontramos se mantiene más o menos igual (mismo signo y que siga siendo significativo) aunque cambiemos un poco el rango de edad, entonces podemos estar tranquilos de que el resultado es real. Si el efecto desaparece o cambia de signo cada vez que movemos el bandwidth, entonces nuestro hallazgo sería muy frágil y habría que tomarlo con pinzas.

Interpretación trade-off: Aquí hay un dilema , si usamos un bandwidth muy chiquito, el análisis es más "puro" porque comparamos a niños que son casi idénticos en edad, pero perdemos precisión porque nos quedamos con poquitas observaciones. En cambio, si abrimos mucho la ventana, tenemos un montón de datos (más precisión), pero corremos el riesgo de meter ruido o sesgo si la relación entre edad y anemia deja de ser lineal. El punto es encontrar ese equilibrio donde el resultado no cambie drásticamente.

---

### 4.6 Gráfico 6: Test de placebo en el umbral (cortes falsos)

![Gráfico 6: Cortes falsos](https://github.com/krlos20203563/TF_Causal_Inference/blob/main/grafico6_placebo_corte.png?raw=true)

Para quitarnos la duda de si el salto que vimos a los 36 meses es real, hicimos una prueba de "lugar falso" o placebo. Básicamente, agarramos un montón de edades distintas (desde los 33.5 hasta los 38.5 meses) y corrimos el modelo RD como si cada una de esas edades fuera el corte real del programa. Después, graficamos cada coeficiente con su respectivo intervalo de confianza al 95%.

¿Qué es lo que esperamos? que el único resultado estadísticamente significativo aparezca justo en el umbral real de 36 meses. Si de pronto encontráramos saltos importantes en los 34 o 37 meses (donde no cambia nada legalmente), querría decir que hay otros factores "ruidosos" o variables confundidoras que están causando esos brincos, y no el programa Qali Warma.

Interpretación: Si el efecto solo "salta" en el corte de los 36 meses y en los demás puntos no pasa nada (o sea, los intervalos de confianza cruzan el cero), entonces tenemos una evidencia súper sólida. Eso nos confirma que el cambio que vimos en la anemia se debe efectivamente al programa y no a una coincidencia o a algo raro que esté pasando con la edad de los niños.
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

El **coeficiente $\hat{\tau}_{RD}$**de nuestro modelo principal representa el cambio en la probabilidad de tener anemia, medido en puntos porcentuales, que ocurre exactamente en el umbral de los 36 meses. Este cambio se atribuye directamente a que el niño empieza a ser elegible para Qali Warma.

- Si $\hat{\tau}_{RD} < 0$ y es estadísticamente significativo: el programa **reduce** la probabilidad de anemia. Por ejemplo, un coeficiente de -0.05 indicaría que ser elegible para el programa reduce la probabilidad de anemia en 5 puntos porcentuales en el umbral.
- Si $\hat{\tau}_{RD}$ no es estadísticamente significativo: no encontramos evidencia de que el programa tenga efecto sobre la anemia en el umbral. Esto puede deberse a que el efecto es genuinamente nulo, o a que la muestra es insuficiente para detectarlo.

**Magnitud del efecto:** Para contextualizar el coeficiente, es útil expresarlo como porcentaje de la prevalencia de anemia en el grupo control. Si la prevalencia de anemia en niños de 33-35 meses es, por ejemplo, del 45%, un coeficiente de -0.05 representaría una reducción relativa de aproximadamente 11%.

**Validez local del estimador:** Es fundamental recordar que el estimador RD solo tiene validez causal para los niños en el margen del umbral (alrededor de 36 meses). No podemos generalizar el resultado a niños de 12 meses ni a niños de 5 años. Este es el costo de la credibilidad causal del diseño.

---

## 6. Pruebas de Robustez

### 6.1 Test de densidad (McCrary)

Para asegurar que nuestro diseño sea válido, aplicamos el test de McCrary, que básicamente sirve para chequear si hubo alguna manipulación en la edad de los niños. La idea es que, si nadie está "forzando" la entrada al programa, la distribución de la edad debería verse continua, sin saltos raros justo en los 36 meses.Si miramos el Gráfico 1 y comparamos cuántos niños hay a cada lado del umbral, podemos ver si existe esa amenaza. Como en nuestro caso no logramos rechazar la hipótesis nula ($H_0$), podemos decir que no hay evidencia de manipulación. Esto es clave porque nos confirma que los grupos se formaron de manera natural por la edad y no porque alguien movió las fechas para calificar al programa.

### 6.2 Tests de placebo en covariables

Tal como vimos en el Gráfico 4, usamos los tests de placebo para confirmar que el umbral de 36 meses no coincide con cambios en otras características de los niños. En la tabla de resultados que generamos en el notebook, se detallan el coeficiente RD y el p-valor para cada variable (como sexo o peso). Lo que buscamos aquí es que ninguna de estas covariables salga significativa, lo que demostraría que los grupos están bien balanceados y que el diseño es válido.

### 6.3 Sensibilidad al bandwidth

En la Tabla 2 y el Gráfico 5 mostramos las estimaciones usando diferentes anchos de ventana. Esta es una de las pruebas de robustez más importantes, ya que un buen resultado no debería depender de un rango de edad específico. Si el signo y el tamaño del coeficiente se mantienen estables cuando variamos el bandwidth entre 1 y 3 meses, podemos confiar en que el efecto encontrado es sólido y no una casualidad de los datos.

### 6.4 Polinomio de orden 2

En la Tabla 3 del notebook comparamos qué pasa cuando usamos un polinomio de orden 1 (lineal) frente a uno de orden 2 (cuadrático). Es una comparación importante porque el orden que elijamos afecta el resultado: un polinomio más alto es más flexible para seguir los datos, pero también vuelve al estimador más inestable. Como ambos modelos nos dan coeficientes parecidos, esto nos indica que la relación entre la edad y la anemia es básicamente lineal dentro del rango que estamos analizando, lo que justifica usar el modelo más simple.

### 6.5 Test de cortes falsos

En el Gráfico 6 comprobamos que el salto en la anemia ocurra solo a los 36 meses y no en otras edades. Como el efecto es significativo en el corte real, pero desaparece si probamos en los 34 o 38 meses, tenemos una prueba más de que el resultado se debe al programa y no a otros factores externos o "ruido" en los datos.

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






