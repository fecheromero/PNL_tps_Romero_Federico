#  Reporte Desafio 3

Utilice el mismo dataset que use en el desafio 2 con todas las canciones de la banda: **Twenty One Pilots**.  Aproximadamente 4195  frases.

## 📊 1. Métricas y Performance

| Modelo | Perplejidad Inicial (E1) | Perplejidad Final | Tiempo / Época | Configuración de Entrada | Estado Final |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **SimpleRNN** | 9.32 | 6.41 | < 60s | TimeDist + One-Hot | Baseline Inestable |
| **GRU (Model 1)** | 9.51 | 6.75 | ~730s | TimeDist + One-Hot | **Éxito?: Coherencia por overfittig** |
| **LSTM (Model 2)** | 11.54 | 8.18 | ~910s | TimeDist + One-Hot | Ruido Simbólico |
| **Doble GRU** | 9.03 | **6.55** | ~890s | TimeDist + One-Hot | Overfitting Local |
| **LstmEmbed_v1** | 10.60 | 7.98 | ~550s | Embedding (64 dim) | Ruido Semántico |
| **Conv_Atten_v1** | 8.41 | 10.67 | **33s** | Embedding + CNN | Bucle (Efecto Loro) |
| **LstmEmbed_v2** | N/A | N/A | ~600s | **Embed(16) + Bidir** | Simbolismo Repetitivo |
| **Conv_Atten_v2** | **23.03** | **113.78** | ~2300s | **CNN + Attention + Dropout**| Colapso / Alucinación |

---

## 🏗️ 2.  Porque se iteraron lstm y conv?


A diferencia de la v1, la **v2** buscó mayor regularización y extracción de características locales antes de la atención aumentando el dropout  en 2 momentos antes de la conv1d y despues  de la misma. Pero los resultados aun asi no fueron mejores.
En cuanto a la LSTM_v2 se redujo la cantidad de dimensiones de 64 a 16 apuntando a evitar generar mucho ruido sobre un dataset chico por sobredimensionar los vectores. Aun asi los resultados tampoco mejoraron. 

---


## 🧪 3. Evaluación Documental de Inferencia

#### 🔹 Semilla 1: `"can you save"` (Secuencia Literal del Dataset)
Esta semilla evalúa la capacidad de reconstrucción y memoria asociativa de los modelos.

**Model: SimpleRNN**
* `Greedy`: can you saveawuAAjAyAwuAAjAyAwuAAjAyAwuAAj
* `Beam det`: can you saveawuAuxî)AOAywaq)Auîa
* `Beam sto 0.7`: can you saveawuAuxî)AOAeawuAm)q.
* `Beam sto 0.3`: can you saveawuAuxî)AOAywaq)Auîa

**Model: GRU**
* `Greedy`: **can you save my heavy dirty soul? Can you**
* * `Beam det`: **can you save my heavy dirty soul**
* `Beam sto 0.7`: **can you save my heavy dirty soul**
* `Beam sto 0.3`: **can you save my heavy dirty soul**

**Model: LSTM**
* `Greedy`: can you savei!iîDKninQinQеinQеinQеinQеinQе
* `Beam det`: can you saveKmhinQ!niéi,!mninQ.i
* `Beam sto 0.7`: can you saveinDinQ.i,!mn.vié=qiD
* `Beam sto 0.3`: can you saveinQ.i,!mninQ.i,!mnin

**Model: DobleGRU**
* `Greedy`: can you savei!mîiéi,!mninDaeniKKK.znKKKKD.
* `Beam det`: can you savei,Kddin!b.iqеiQ.!vni
* `Beam sto 0.7`: can you saveiKminQ.ieavvDamî.îiK
* `Beam sto 0.3`: can you savei!mîinQ.i?vD)d.qei!m

**Model: LstmEmbedding**
* `Greedy`: can you savem—NNBSSSSSSSSSSSSSSSSSSSSSSSSS
* `Beam det`: can you savem—N—jm—NKt((NpIxgNKt
* `Beam sto 0.7`: can you savem—N—jSNUS——oIGNoIN—j
* `Beam sto 0.3`: can you savem—N—jSNUSSIN—jSNgo((

**Model: AttentionCNN (v2)**
* `Greedy`: can you saveuygo-yu b5y! b5y! b5y! b5y! b5
* `Beam det`: can you save vk vk vk vk vk vk v
* `Beam sto 0.7`: can you save b5y mky.g-Y5Cy mky 
* `Beam sto 0.3`: can you save b5yB AyB AyB AyB Ay

---

#### 🔸 Semilla 2: `"Nico will"` (Concepto Temático / No Literal)
Esta semilla evalúa la capacidad de generalización semantica.

**Model: SimpleRNN**
* `Greedy`: Nico willqqr(Bxеrlwsrtzxr?lwsrtzxr:’t3r
* `Beam det`: Nico willtxеtl’wrP3rzxlеtr(Br
* `Beam sto 0.7`: Nico willqq’wFrs(Tw'r3xlz'r3x
* `Beam sto 0.3`: Nico willtxеrlwsrtzxrslеLrlws

**Model: GRU**
* `Greedy`: Nico willcwWjcZ=jv=jfcAjALc('wJcRcvYLcR
* `Beam det`: Nico willcvAjHJc(wjjwcZwWWqcZ
* `Beam sto 0.7`: Nico willcvAjHJc(wjjwcZwWWcw(
* `Beam sto 0.3`: Nico willcwWWcRHFLcLFLYcbLLjc

**Model: LSTM**
* `Greedy`: Nico will=pnw=Ln’,j=qó’j=jh?=Ln’,j=qó’j
* `Beam det`: Nico will=pnw=ln’,j=b’nq=pnw=
* `Beam sto 0.7`: Nico will=pnw=b’nq=pnw,F?=Ln’
* `Beam sto 0.3`: Nico will=pnw=ln’,j=jh?=b’nq=

**Model: DobleGRU**
* `Greedy`: Nico will=pnw=b’nq=jhój= =qó’j=jn=Ln= =
* `Beam det`: Nico will=pnw=b’nq=jhój=oj,L=
* `Beam sto 0.7`: Nico will=pnw=b’nq=jhój= =qó’
* `Beam sto 0.3`: Nico will=pnw=b’nq=jhój= =qó’

**Model: LstmEmbedding**
* `Greedy`: Nico willwJcJALLLcJALLLcJALLLcJALLLcJAL
* `Beam det`: Nico willw5vw5vw5vw5vw5vw5vw5
* `Beam sto 0.7`: Nico willw5vw5vw5vw5vw5vw5vwq
* `Beam sto 0.3`: Nico willw5vw5vw5vw5vw5vw5vw5

**Model: AttentionCNN (v2)**
* `Greedy`: Nico willezHeeeGHbaaG=zeVF,?—i?lezeVe=V
* `Beam det`: Nico willezHNlezHNlezHNlezHNl
* `Beam sto 0.7`: Nico willezHe=zeeeHGweeHG=.Yl
* `Beam sto 0.3`: Nico willezHe=zeeeHGVrleV=eVV

---

## 🔍 4. Análisis, Hallazgos y Conclusiones

* El modelo que usa CNN + atencion v2 alcanzó un **accuracy del 99.6%** y un **loss de 0.0122**, pero la **perplejidad final de 113.78**. Los resultados fueron decepcionates, la hipotesis es que el dataset es muy chico como para poder aprender con enmbeddings que quizas quearon grandes en cuanto al a cantidad de parametros, rapidamente entro en secuencias repetitivas.
* La **GRU** logra terminar la frase "Can you save"  porque funciona como una memoria asociativa simple. Al ser una frase recurrente, recorre un camino estadístico ya grabado. Al darle una frase que no forma parte exacta de lo aprendido hizo agua como el resto. Da cuenta de overfitting.
* En general el beam estocastico con menor temperatura redujo la presencia de caracteres especiales. Aun asi, no logro genrear palabras con sentido.
### 4.4 Conclusión Final: Simplicidad vs. Complejidad
Decepcionantemente luego de pasar mas 5hs entrenando algunos de los modelos de ejemplo no he logrado romper la barrera de la perlejidad de la simple RNN de 6.41. Logrando en uno solo de los ejemplos palabras coherentes. 
Para datasets de nicho (letras de una sola banda), las arquitecturas **Recurrentes (GRU)** demostraron ser superiores (aunque solo logran repetir como loros). Las arquitecturas de **Atención** son extremadamente sensibles: sin una cantidad masiva de datos o un Positional Encoding perfecto, tienden a memorizar el ruido (overfitting) mucho más rápido que las RNN, como demuestra el accuracy de 99% que no se traduce en lenguaje humano.
