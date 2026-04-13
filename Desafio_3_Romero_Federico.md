# Desafio 3 

Este documento presenta las conclusiones definitivas tras el entrenamiento y evaluación de diversos modelos de Deep Learning sobre un dataset de aproximadamente 60 canciones (4,915 versos). Para simplicidad de las pruebas y la ejecución se le asigno un "alias" a cada modelo. La definición tecnica de cada modelo se puede encontrar en el collab. Se vera en el collab a su vez codigo utilizado para guardar los modelos entrenados en drive y cargarlos desde ahi, esto fue necesario dado que los entrenamientos se extendian a veces por encima de las 6 hs y recuperar algunos modelos pre entrenados fue necesario para poder llegar a evaluar todo antes de perder la instancia de T4.

##  Tabla Comparativa de Performance Final

| Modelo | Perplejidad Final | Perplejidad epoch 1 |  Inferencia Destacada ("can you save...") | Estado |
| :--- | :--- | :---  |  :--- | :--- |
| **SimpleRNN** | 6.69 | 9.6 | `...the want the want the want` | Estable / Limitado |
| **GRU**  | 7.16 | 9.08 | `...my soul the street` | Temático / Involutivo |
| **LSTM**  | 8.50 | 12.4 | `...the the the the the` | Colapso de Varianza |
| **Doble GRU** | **6.49** | 9.3 | `...you the the the the` | Estable / Conservador |
| **LstmEmbed** | 6.84 | 11.8 | `...my head to stay to the start` | **Máxima Coherencia** |
| **ConvAtten** | 7.82 | 9.4 | `...me for me the for me` | Rítmico / Bucle |


---

##  2. Análisis del Comportamiento de la Perplejidad

En un dataset reducido (~60 canciones), la perplejidad actuó como un indicador crítico de la "salud" del aprendizaje. Observamos tres comportamientos distintos:

###  La Involución de la GRU (Overfitting Prematuro)
La GRU comenzó con una perplejidad excelente (6.60 en E4) pero terminó subiendo a 7.16. 
* **Interpretación:** Memorizó rápidamente secuencias dominantes. El modelo dejó de generalizar para intentar replicar el dataset.

###  El Valle de Optimización del LstmEmbedding
Este modelo alcanzó su punto óptimo en la Época 16 (6.61). Las épocas posteriores redujeron el *loss* pero aumentaron la perplejidad.
* **Interpretación:** El espacio latente del embedding (capa de 90 unidades) logró capturar la estructura de las palabras, pero tras 16 iteraciones, empezó a "especializarse" en el ruido estadístico del texto (muletillas, errores de puntuación), perdiendo calidad predictiva.

### Estabilidad en Doble GRU
Fue el modelo con la perplejidad más baja y constante. Pero aun asi los resultados fueron decepcionantes: quedo repitiendo articulos sin poder generar frases con sentido.

##  Variación en cantidad de unidades

Se han iterado los modelos con menos neuronas (15-25) y hasta 180-200. Los mejores resultados: generación de texto cercana a frases con sentido, se han obtenido con unidades entre 90 y 100 dependiendo el modelo. En la mayoria de los casos anteriores o se llegaba a repetición de articulos o gran cantidad de simbolos de puntuación perdidos en las frases.

---

##  3. Análisis Semántico de Resultados

###  El Éxito del Embedding (LstmEmbedding)
Fue el único modelo capaz de generar una estructura lírica creíble: `"can you save my head to stay to the start"`. 
* **Interpretación:** Al no usar One-Hot, el modelo pudo agrupar caracteres similares en un espacio vectorial. Esto le permitió "entender" que después de un verbo suele venir un pronombre o un sustantivo, manteniendo la coherencia gramatical incluso en frases que no existen textualmente en el dataset.

El GRU simple logro a su vez frases que podrían  acercarse a tener cierto sentido `"can you save that I want to the"` pero el lstmEmbedding claramente gano en la composición lírica y semántica. 

###  El "Modo Tartamudo" (LSTM, DobleGRU y conv1 con atención)
Ambos colapsaron en la repetición de articulos o preposiciones como "the" y "for". 
* **Interpretación:** En el inglés de las letras de canciones, estos tokens tienen mucha probabilidad estadística. Si el modelo no tiene suficiente neuronas o aleatoriedad para saltar a la siguiente palabra probable, se queda atrapado en el camino de menor resistencia.

---


## 📌 Conclusión Final
Para un corpus de 60 canciones, la arquitectura **LstmEmbedding (~90-100 unidades)** es la ganadora absoluta. Logró trascender la simple repetición de caracteres para construir imágenes poéticas alineadas con el estilo original.  Mientras que modelos de mayor capacidad se perdieron en el overfitting, incluso el modelo que dio menor perplejidad (GRU Simple).

---

## Glosario

### grafico de perplejidad
<img width="567" height="455" alt="image" src="https://github.com/user-attachments/assets/021a1111-730e-45ea-b41e-5a3b8eacca17" />
<img width="567" height="455" alt="image" src="https://github.com/user-attachments/assets/5077b7c5-30a0-482c-8809-f6cab25bea12" />
<img width="576" height="455" alt="image" src="https://github.com/user-attachments/assets/94b7bf68-6ed4-4dfa-93e0-1ededb0a22ba" />
<img width="567" height="455" alt="image" src="https://github.com/user-attachments/assets/e0489924-deba-44af-a664-350e68abb317" />
<img width="563" height="455" alt="image" src="https://github.com/user-attachments/assets/8217e72f-021c-4172-8bcd-074e98147eb1" />
<img width="576" height="455" alt="image" src="https://github.com/user-attachments/assets/38deb174-3383-43f3-b3ed-9c1d96b31138" />

### pruebas 


**Model: SimpleRNN**

  Greedy: can you save the want the want the want th
  
  Beam det: can you save you have you don't 
  
  Beam sto 0.7: can you save in the lave you say
  
  Beam sto 0.3: can you save you are the with yo

--- 

**Model: GRU**

  Greedy: can you save my soul the street In the str
  
  Beam det: can you save that I want to the 
  
  Beam sto 0.7: can you save the someone else's 
  
  Beam sto 0.3: can you save my sounds to start 

--- 

**Model: LSTM**

  Greedy: can you save the the the the the the the t
  
  Beam det: can you save the the the the the
  
  Beam sto 0.7: can you save the I the se the Io
  
  Beam sto 0.3: can you save the the the the the

--- 

**Model: DobleGRU**

  Greedy: can you save the the the the the the the t
  
  Beam det: can you save you the the the the
  
  Beam sto 0.7: can you save the me the se sime 
  
  Beam sto 0.3: can you save the the the the to 

  --- 

**Model: LstmEmbedding**

  Greedy: can you save my head to stay to the start 
  
  Beam det: can you save, they know what you
  
  Beam sto 0.7: can you save, the will the stop,
  
  Beam sto 0.3: can you save my head to the star

  --- 

**Model: AttentionCNN**

  Greedy: can you save me for me the for me the for 
  
  Beam det: can you save you thing tore the 
  
  Beam sto 0.7: can you save you the the the for
  
  Beam sto 0.3: can you save you the for me the 


