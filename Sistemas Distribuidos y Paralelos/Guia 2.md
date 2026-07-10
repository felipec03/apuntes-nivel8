## 1. Modelado Teórico (Amdahl, Gustafsson, Speedup, Eficiencia)
### Ejercicio 1: Amdahl aplicado a offloading CUDA

> Un algoritmo tiene: 
> - $15\%$ secuencial fijo en CPU (pre/post-procesamiento).
> - $85\%$ potencialmente acelerable en GPU.
> Considere $P = \{128, 256, 512, 1024, 2048\}$ unidades efectivas para la parte paralela.

(a) Calcule $S(P)$ con Amdahl.
Siendo $s = 0.15$
- $S(128) = \frac{1}{0.15 + \frac{0.85}{128}}$
- $S(256) = \frac{1}{0.15 + \frac{0.85}{256}}$
- $S(512) = \frac{1}{0.15 + \frac{0.85}{512}}$
- $S(1024) = \frac{1}{0.15 + \frac{0.85}{1024}}$
- $S(2048) = \frac{1}{0.15 + \frac{0.85}{2048}}$

(b) Calcule $E(P)$.


(c) Determine el límite $S_{max}$ cuando $P \to \infty$.

$$
S_{max} = \lim_{p \to \infty} \frac{1}{s + \frac{1-s}{p}} = \frac{1}{s} \iff \frac{1}{0.15} \approx 6.6
$$

(d) Interprete por qué aumentar mucho $P$ no entrega ganancias proporcionales.
Idem a paralelismo con CPU, al aumentar $P$ está capeado por la fracción secuencial.

## Ejercicio 2. Amdahl con overhead de transferencia
> Para un kernel de suma de vectores:

$$T_{total,CUDA}(N) = T_{H2D}(N) + T_{kernel}(N) + T_{D2H}(N)$$
> con:

$$T_{H2D} = 0,2 + 0,0008N, \ T_{kernel} = 0,0006N, \ T_{D2H} = 0,2 + 0,0008N$$
> (tiempos en ms), y $T_{seq}(N) = 0,01N$ ms.

(a) Calcule el speedup total para $N = 10^4, 10^5, 10^6, 10^7$.
Sabiendo que:
$$
S(N) = \frac{T_{seq}}{T_{total, CUDA(N)}} = \frac{0.01N}{0.2+0.0008N + 0.0006N + 0.2 + 0.0008N} = \frac{0.01N}{0.4 + 0.0022N}
$$

|**Tamaño de N**|**Tseq​ (ms)**|**Ttotal,CUDA​ (ms)**|**Speedup Total (Stotal​)**|
|---|---|---|---|
|**$10^4$**|100|22.4|**4.46**|
|**$10^5$**|1000|220.4|**4.54**|
|**$10^6$**|10000|2200.4|**4.54**|
|**$10^7$**|100000|22000.4|**4.54**|
*Me dio mucha paja hacerlo yo se lo pedí al gemini*

(b) Calcule speedup considerando solo kernel.
Si consideramos solo kernel...
$$
S_{kernel} = \frac{0.01N}{0.0006N} \approx 16.667
$$

(c) Estime la fracción secuencial efectiva $s(N) = \frac{T_{H2D}+T_{D2H}}{T_{total},CUDA}$.

Viendo bien la fórmul, es el tiempo que se demora ir de host a dispositivo y de dispositivo a host, osea, todo el overhead que tiene CUDA, dividido en el total, por ende representa bien la fracción secuencial.
$$s(N) = \frac{T_{H2D} + T_{D2H}}{T_{total,CUDA}} = \frac{0.4 + 0.0016N}{0.4 + 0.0022N}$$

|**Tamaño de N**|**Fracción Secuencial s(N)**|**Porcentaje (%)**|
|---|---|---|
|**$10^4$**|16.4 / 22.4|**73.21%**|
|**$10^5$**|160.4 / 220.4|**72.77%**|
|**$10^6$**|1600.4 / 2200.4|**72.73%**|
|**$10^7$**|16000.4 / 22000.4|**72.72%**|

*igual me dio paja se lo di al gemini*

(d) Discuta si Amdahl o Gustafsson describe mejor el comportamiento al crecer N.
Cómo depende del tamaño del problema, uno pensaría que Gustafsson sería el modelo ideal para representar el problema, pero basta con ver los resultados del speedup, donde al aumentar el tamaño del problema, vemos que el speedup se mantiene igual idem a la fracción secuencial.
## Ejercicio 3. Ley de Gustafsson con tamaño de problema creciente

> Un algoritmo en GPU escala el tamaño de entrada con P, manteniendo tiempo total cercano a constante. Se mide $s(W) = 0,06$ para $P = 256$, $s(W) = 0,04$ para $P = 512, s(W) = 0,025$ para $P = 1024$.

(a) Calcule $S(P, W) = \frac{P}{1+(P −1) s(W)}$ para cada caso.

- **Para $P=256$ y $s(W)=0.06$**:
$$S(256, W) = \frac{256}{1 + (255 \times 0.06)} = \frac{256}{1 + 15.3} = \frac{256}{16.3} \approx \textbf{15.71}$$
- **Para $P=512$ y $s(W)=0.04$**:
$$S(512, W) = \frac{512}{1 + (511 \times 0.04)} = \frac{512}{1 + 20.44} = \frac{512}{21.44} \approx \textbf{23.88}$$
- **Para $P=1024$ y $s(W)=0.025$**:
$$S(1024, W) = \frac{1024}{1 + (1023 \times 0.025)} = \frac{1024}{1 + 25.575} = \frac{1024}{26.575} \approx \textbf{38.53}$$

(b) Compare con Amdahl usando $s = 0,06$ fijo para todos los $P$.
(c) Explique el impacto de disminuir la fracción secuencial al crecer el problema.
(d) Concluya cuándo usar Amdahl y cuándo Gustafsson en CUDA.