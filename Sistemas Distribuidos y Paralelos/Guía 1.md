# Ley de Amdahl, Ley de Gustafson y speedup
## Repaso
Para $I_c$ instrucciones dinámicas, $CPI$ y frecuencia de reloj $f_{clk}$ el modelo o rendimiento del CPU ($T$) se describe cómo
$$
T = \frac{I_c \cdot CPI}{f_{clk}} = size \times CPI \times cycleTime
$$
El **speedup** relaciona los tiempos de reloj de pared en base a una referencia experimental, para $p$ recursos paralelos, **tiempo viejo partido tiempo nuevo.**
$$
S(p) = \frac{T(1)}{T(s)}
$$
Ahora, la **Ley de Amdahl** es un principio que se usa para **predecir la aceleración máxima teórica de una tarea cuando una porción es paralelizable.**
Siendo $f$ la **fracción NO paralelizable**, naturalmente $1-f$ es la sección paralelizable.
$$
S(p) \leq \frac{1}{f + \frac{1-f}{p}}
$$
Mientras más procesadores se añadan, osea $f \rightarrow \infty$...
$$
S(p) \leq \frac{1}{f}
$$
Notar que strong scaling es que el largo del problema (tarea) se mantiene estático, siendo el único parámetro que se mueve $p$. Por ende $f$, se convierte en el cuello de botella.

Ahora, le **Ley de Gustafson**, para un tiempo $T_s$ igual a tiempo no paralelo, y $T_p$ igual al tiempo paralelo **para una sola CPU** se puede relacionar:
$$
S_{G}(p) = \frac{T_s + p T_p}{T_s + T_p}
$$A diferencia de Amdahl, se dice que **Gustafson es optimista**, vale decir que con la cantidad de procesadores (recursos de computo) añadidos escala el problema con el, cómo ejemplo, si tengo 10 procesadores neto, el problema se vuelve 10 veces más grande, ya que Gustafson asume que la parte serializable escaló 10 veces con la cantidad de procesadores (carga de datos, IO, etc...) -> Aplica para optimización con carga escalable.

***
## Ejercicios Teóricos
### Ejercicio 1. Amdahl y notación
> Un programa en la referencia de un CPU tiene fracción serial $f = 0,12$ ($12\%$ del tiempo no paraleliza) y el resto es perfectamente paralelizable en ideal sin overhead.


*(a) Escriba S(p) según Amdahl y calcule $S(2$), $S(4)$, $S(8)$, $S(16)$ y $S(32)$.*
$$
S(p) = \frac{1}{0.12+ \frac{1-0.12}{p}} = \frac{1}{0.12 + 0.88 \cdot \frac{1}{p}}
$$
- $S(2) = 1.79$
- $S(4)= 2.94$
- $S(8)=4.35$
- $S(16)=5.71$
- $S(32)=6.78$

*(b) ¿Cuál es $\lim_{p \to \infty} S(p)$?*
$$
S(\infty) = \frac{1}{0.12} = 8.\overline{3}
$$

*(c) Si $T(1) = 150[s]$ y el modelo ideal de Amdahl aplica, ¿cuánto vale $T(8$)?*
$$S(8) = \frac{T(1)}{T(8)} \iff T(8) = \frac{150}{4.35}=34.48[s]$$

*(d) Grafique $S(p)$ vs. $p$ y comente la saturación respecto de $1/f$.*
Se acerca asintóticamente al tope teórico de $8.3$

### Ejercicio 2. Strong vs. weak y Amdahl vs. Gustafson

> Un ordenamiento domina con $O(N log N)$ trabajo útil; inicialización y recogida de resultados suman un término $O(1)$ en tiempo despreciable frente a $N$ grande.

*(a) Para $N = 2 · 10^3$ , suponga medido $f = 0,06$ (fracción serial del tiempo en un CPU). Calcule la cota de Amdahl S(16).*
$$
S(16) = \frac{1}{0.06 + \frac{1-0.06}{16}} = 8.42
$$

*(b) Para $N = 5 · 10^6$, suponga $f = 0,002$. Calcule de nuevo $S(16)$ con Amdahl.*

$$
S(16) = \frac{1}{0.002 + \frac{1-0.002}{16}} = 15.53
$$

*(c) Explique por qué comparar solo Amdahl con $f$ fija no describe un experimento de weak scaling donde $N$ crece con $p$. En una oración, relaciónelo con la ley de Gustafson.*
Ya que Amdahl no toma en cuenta tamaño del problema, no hay un $N$ asociado, a diferencia de Gustafson que es otro punto de vista, "si añado mas procesadores tengo que hacer más trabajo". En el fondo, weak scaling agranda el problema con mas procesadores y strong scaling lo deja estático limitado por la fracción no paralelizable.

*(d) Con el $f$ del ítem (a), calcule el speedup escalado de Gustafson $S_G(p) = f + (1 − f)p$ para $p = 16$ (recuerde: es otra convención de reporte, no el mismo cociente $\frac{T(1)}{T(p)}$con el mismo $N$ que en (b)).*
$$
S_G(p) = 0.06 + (1 - 0.06)\times 16
$$
"Este $15,1$ representa el _speedup_ que lograrías en un escenario de escalabilidad donde asumes que el trabajo paralelo dominó la ejecución al escalar los recursos a 16 procesadores."
### Ejercicio 3. Overhead lineal en p y eficiencia

> Modelo didáctico (como en clase, con overhead agregado): tiempo de pared

$$
T(p) = f \cdot T_{ref} + \frac{(1-f)T_{ref}}{p} + \alpha \cdot p
$$

>con $T_{ref} = 120[s]$, $f = 0.1$, $\alpha = 0,03[s]$ (costo creciente con $p$, por ej. sincronización o contención).


*(a) Escriba $S(p) = Tref/T(p)$ y $E(p) = S(p)/p$.*
- Speedup:
$$
S(p) = \frac{120}{120 \cdot 0.1 + \frac{0.9 \cdot 120}{p} + 0.03 \cdot p} = \frac{120}{12 + \frac{108}{p} + 0,03p}
$$
- Eficencia:
$$
E(p) = \frac{120}{120 \cdot 0.1 + \frac{0.9 \cdot 120}{p} + 0.03 \cdot p} \cdot \frac{1}{p} = \frac{120}{12p + 108 + 0,03p^2}$$


*(b) Calcule $S(p)$ y $E(p)$ para $p \in {2, 4, 8, 16, 32}$.*
- **Para $p=2$:**
    - $T(2) = 12 + 54 + 0,06 = \textbf{66,06 s}$
    - $S(2) = 120 / 66,06 \approx \textbf{1,82}$
    - $E(2) = 1,82 / 2 \approx \textbf{0,91}$ (**91%**)
- **Para $p=4$:**
    - $T(4) = 12 + 27 + 0,12 = \textbf{39,12 s}$
    - $S(4) = 120 / 39,12 \approx \textbf{3,07}$
    - $E(4) = 3,07 / 4 \approx \textbf{0,77}$ (**77%**)
- **Para $p=8$:**
    - $T(8) = 12 + 13,5 + 0,24 = \textbf{25,74 s}$
    - $S(8) = 120 / 25,74 \approx \textbf{4,66}$
    - $E(8) = 4,66 / 8 \approx \textbf{0,58}$ (**58%**)
- **Para $p=16$:**
    - $T(16) = 12 + 6,75 + 0,48 = \textbf{19,23 s}$
    - $S(16) = 120 / 19,23 \approx \textbf{6,24}$
    - $E(16) = 6,24 / 16 \approx \textbf{0,39}$ (**39%**)
- **Para $p=32$:**
    - $T(32) = 12 + 3,375 + 0,96 = \textbf{16,335 s}$
    - $S(32) = 120 / 16,335 \approx \textbf{7,35}$
    - $E(32) = 7,35 / 32 \approx \textbf{0,23}$ (**23%**)

*(c) Estime por búsqueda o derivación el $p$ que maximiza $S(p)$ en este modelo (puede no*
*ser potencia de 2).*

Si minimizamos $T(p)$ es virtualmente lo mismo que si maximizamos $S(p)$, cómo tienen una relación inversa $S(p) = 120 \times T(p)^{-1}$

Para $T(p) = 12 + \frac{108}{p} + 0,03p$, sacamos $\frac{dT}{dp} = 0$
$$
\frac{dT(p)}{dp} = 0 \iff T'(p) = -108p^{-2} + 0,03 = 0
$$
Ahora, despejando $p$, tenemos que $p =60$

*(d) Comente cuándo $E(p) \ll 1$ y si tendría sentido reportar superlinealidad en este modelo idealizado.

- Para un $p$ muy grande, $E(p)$ va a ir disminuyendo, cómo vemos en los cálculos manuales se va achicando la eficencia, el overhead es exponencialmente influenciado por la cantidad de procesadores. 
- Superlinelidad implica $S(p) > p$, entonces $E(p) > 1$. 

### Ejercicio 4. Matrices: orden del trabajo y escalabilidad

>Sea $T(1, N) = 2N^3+80$ (unidades de tiempo; el término $80$ modela fase serial fija). Paralelo ideal con overhead:

$$
T(p, N) = \frac{2N^3}{p} + 80 + 6p
$$

*(a) Calcule $S(p) = T(1, N)/T(p, N)$ para $N = 120$ y $p \in \{4, 8, 16\}$.*
$$
T(1, 120) = 2\times120^3 + 80
$$
$$
T(p, 120)=2\times \frac{120^3}{p} + 80 + 6p
$$
$$
S(p) = \frac{3.456.080}{3.456.000 \times p^{-1} + 6p}
$$
- **Para $p=4$:**
    - $S(4) = \frac{3.456.080}{864.104} \approx \mathbf{3,9996}$
- **Para $p=8$:**
    - $S(8) = \frac{3.456.080}{432.128} \approx \mathbf{7,9978}$
- **Para $p=16$:**
    - $S(16) = \frac{3.456.080}{216.176} \approx \mathbf{15,987}$

*(b) Repita para $N = 900$ con los mismos $p$.*
$$2(900)^3 = 2(729.000.000) = 1.458.000.000$$
Tiempo secuencial $T(1, 900) = \mathbf{1.458.000.080}$.
- **Para $p=4$:**
    - $T(4) = 364.500.000 + 80 + 24 = \mathbf{364.500.104}$
    - $S(4) = \frac{1.458.000.080}{364.500.104} \approx \mathbf{3,99999}$
- **Para $p=8$:**
    - $T(8) = 182.250.000 + 80 + 48 = \mathbf{182.250.128}$
    - $S(8) = \frac{1.458.000.080}{182.250.128} \approx \mathbf{7,99999}$
- **Para $p=16$:**
    - $T(16) = 91.125.000 + 80 + 96 = \mathbf{91.125.176}$
    - $S(16) = \frac{1.458.000.080}{91.125.176} \approx \mathbf{15,9999}$

**Nota:** Fíjate que al aumentar la carga $\mathcal{O}(N^3)$, el peso de la parte serial fija (80) y del overhead lineal ($6p$) se vuelve matemáticamente insignificante, por lo que el _speedup_ es prácticamente ideal.

*(c) Discuta strong scaling fijando $N$ y subiendo $p$: ¿en qué régimen domina $\frac{2N^3}{p}$ y*
*cuándo domina $6p$?*



*(d) Proponga un experimento de weak scaling (p. ej. $N$ proporcional a $p$) y diga qué
observaría en $T(p, N)$ si el término dominante del trabajo fuera $O(N^3/p)$.*

### Ejercicio 5. Repaso: CPI y FLOP/s (opcional, corto)
> Si $I_c = 8 · 10^8$ , $CPI = 6$ y $f_{clk} = 2,5 GHz$, calcule $T$. Si ese programa ejecuta $4 · 10^9 FP$ en ese tiempo, ¿cuántos GFLOP/s reportaría?

$$
T = \frac{8 \cdot 10^8 \times 6}{2,5 \times 10^9} = 1.92[s]
$$
Y, GFLOP/s se calcula con la cantidad de operaciones en el tiempo que se demora
$$
GFLOP/s = \frac{4}{1.92} = 2.083 [\frac{GFLOP}{s}]
$$
### Ejercicio 6. Lectura de time (opcional)
> Un proceso imprime `real` $40[s]$, `user` $120[s]$, `sys` $5[s]$. ¿Puede ser coherente en una máquina de 4 núcleos? Explique en una frase la diferencia entre real y user.


# Concurrencia General
## Repaso
- **Condición de carrera**: Dos o más hebras acceden a sección crítica por lo general modificando un recurso compartido.
- **Sincronización**: Barrera, mutex, semáforo, locks...
- **Deadlock**: Cuando dos o más hebras tienen recursos bloqueantes para las demas hebras
- **Overhead de creación**: Tiempo que se demora crear/planificar hebras.
### Ejercicio 7. Análisis de Speedup en Programas Concurrentes

> Considere un programa que procesa $M$ elementos independientes. La versión secuencial procesa cada elemento en $t$ segundos. La versión concurrente crea $n$ hilos, cada uno procesando aproximadamente $M/n$ elementos, pero introduce un overhead de sincronización de $o$ segundos por hilo.

(a) Derive la fórmula del speedup considerando el overhead.
$$
S(n) = \frac{T(1)}{T(n)} = \frac{t M}{t\frac{M}{n} + on}
$$

(b) Para $M = 1000$, $t = 0.1$ segundos y $o = 0,05$ segundos, calcule el speedup para
$n = 2, 4, 8, 16$.
- **Para $n=2$:**
    - $S(2) = \frac{100}{50,1} \approx \textbf{1,996}$
- **Para $n=4$:**
    - $S(4) = \frac{100}{25,2} \approx \textbf{3,968}$
- **Para $n=8$:**
    - $S(8) = \frac{100}{12,9} \approx \textbf{7,752}$
- **Para $n=16$:**
    - $S(16) = \frac{100}{7,05} \approx \textbf{14,184}$

(c) Identifique la fracción secuencial y paralelizable según la Ley de Amdahl.


(d) Determine el número óptimo de hilos que maximiza el speedup.


(e) Discuta cómo el overhead afecta la aplicabilidad de la Ley de Amdahl.

