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
Anunciando ley de Amdahl:
$$
S(n) \leq \frac{1}{f + \frac{1-f}{n}}
$$
Sabiendo que $M=1000$, $t=0.1$, para $n=8$ y $o=0.05$
$$
\frac{100}{\frac{100}{8} + 0.05\cdot8} = \frac{1}{f + \frac{1-f}{8}}
$$
(d) Determine el número óptimo de hilos que maximiza el speedup.
Idem a anterior, derivar a igualar a 0, si tomamamos $t, M, n$ cómo constantes.

$$
\frac{dS(n)}{dn} = \frac{d}{dn} (\frac{M t n}{Mt + on^2}) = 0
$$

Sabiendo que $M=1000$, $t=0.1$, para $n=8$ y $o=0.05$
$$
\frac{dS(n)}{dn} = \frac{d}{dn} (\frac{100n}{t\frac{M}{n} + on}) = 0
$$
Más todavía, sería equivalente minimizar el tiempo, por ende:
$$
\frac{dT(n)}{dn} = 0 = \frac{d(t\frac{M}{n} + on)}{dn} \iff \frac{d}{dn}(\frac{100}{n} + 0.05n) = 0 \iff -100n^{-2} + 0.05 = 0
$$
Despejando $n$
$$
-\frac{100}{n^2} + 0,05 = 0 \iff \frac{100}{n^2} = 0,05 \iff n = \sqrt{2000} \approx 44,72
$$
Finalmente, el óptimo, cómo no se pueden tener 44.7 hebras, el óptimo serían 45 hebras.

(e) Discuta cómo el overhead afecta la aplicabilidad de la Ley de Amdahl.
Viendo la formula tenemos que:
$$
S(n)=\frac{M t n}{Mt + on^2}
$$
Por ende, mientras más hebras se vayan introduciendo, el overhead va a influir de manera cuadrática inversa respecto del speedup.
### Ejercicio 8. Modelado de Overhead de Sincronización
> Un programa concurrente tiene las siguientes características: 
> - Tiempo de cómputo puro: $Tcomp = 100$ segundos ($90\%$ paralelizable) 
> - Tiempo de sincronización: $Tsync(n) = 5 log2(n)$ segundos
> - Tiempo de creación de hilos: $Tcreate(n) = 0,1 · n$ segundos

(a) Escriba la fórmula completa del tiempo de ejecución paralelo. 
Del enunciado sabemos que $f = 0.9$ y $1-f =0.1$
$$
S(n) = \frac{1}{f + \frac{1-f}{n}} = \frac{100}{10 +\frac{90}{n} + 5 \log_2(n) + 0.1n} = \frac{T(1)}{T(n)}
$$
Realmente el tiempo paralelo es:
$$
T(n) = 10 +\frac{90}{n} + 5 \log_2(n) + 0.1n
$$
Sabiendo que:
- El tiempo de cómputo total es $100$, siendo un $0.9$ paralelizable, son $90$ segundos paralelos
- El tiempo de cómputo total es $100$, siendo un $0.1$ no paralelizable, son $10$ segundos seriales
- Y sumando el overhead de sincronización y creación de hilos.

(b) Calcule el speedup real para $n = 2, 4, 8, 16, 32$. 
(c) Compare con el speedup ideal de Amdahl (sin overhead). 
(d) Analice el punto donde el overhead supera los beneficios del paralelismo.

### Ejercicio 9. Balanceo de Carga y Speedup
> En un programa concurrente con $n$ hilos, la carga puede estar desbalanceada. Suponga que:
- Hilo $1$ procesa $w_1$ = $60$ unidades de trabajo
- Hilos $2$ a $n$ procesan $w_i$ = $40$ unidades cada uno
> Cada unidad toma $1$ segundo

*(a) Calcule el tiempo de ejecución paralelo considerando que todos los hilos deben terminar.*
Hay que tomar en cuenta el tiempo de ejecución de la primera hebra, luego agregar las otras hebras.
$$
T(n) = 60 \cdot 1[s] + 40n \cdot 1[s]
$$
Según el solucionario es:
$$
T(n) = \max{(W)}
$$
Con
$$
W = \{w_1, w_2, \dots, w_n \}
$$
Por ende sería el cuello de botella, la primera hebra 
$$T_p = T(n) = 60[s]$$

*(b) Calcule el speedup real vs. el speedup ideal (carga balanceada).*
**Para el speedup real:**
$$
S(n) = \frac{T(1)}{T(n)}
$$
Ahora, se saca el trabajo total con una hebra
$$
T(1) = 60 + \sum_{i=2}^n 40 = 60 + 40(n-1) = 40n + 20.
$$
Con el resultado anterior entonces:
$$
S(n) = \frac{T(1)}{T(n)} = \frac{40n + 2}{60}
$$

**Para el speedup ideal:**
La fracción no paralelizable es únicamente la primera hebra, que pasa a ser $f=0$
$$
S(n) = \frac{1}{f + \frac{1-f}{n}} \iff \frac{1}{0+ n^{-1}} = n
$$
También se puede hacer con la otra fórmula sabiendo que el tiempo para n hebras ideal es
$$
T_{ideal}(n) = \frac{40n+20}{n}
$$
Osea, que se pueda repartir el trabajo total equitativamente en $n$ hebras
$$
S(n) = \frac{T(1)}{T(n)} = \frac{40n + 20}{\frac{40n + 20}{n}} = n
$$
*(c) Derive una fórmula general para el speedup con desbalanceo.*
Si tomamos nuevamente 
$$T(n) = \max{w_n}$$
Y además para, 
$$T(1) = \sum_{1}^{n} w_i$$
Entonces
$$
S(n) = \frac{\sum_{i=1}^{n} w_i}{\max(w_1, w_2, ..., w_n)}
$$

*(d) Discuta cómo el desbalanceo afecta la Ley de Amdahl.*

# Open MP
## Conceptos Fundamentales

>[!INFO]
>OpenMP es una API para implementar paralelismo en memoria compartida.

- Se forman equipos de hebras -> La inicia una hebra maestra.
- Ejecutan el mismo código de bloque
## Ejercicios

### Ejercicio 10. Análisis de Código: Suma de Vectores

> Analice el siguiente código OpenMP que suma dos vectores:

```c++
# include <omp.h>
void vector_sum(double *a, double *b, double *c, int n) {
	# pragma omp parallel for
	for (int i = 0; i < n; i++) {
		c[i] = a[i] + b[i];
	}
}
```

(a) Identifique la fracción paralelizable y secuencial según la Ley de Amdahl. ¿Cuál es el
speedup máximo teórico con $n$ hilos?
Recordando Ley de Amdahl:
$$
S(n) \leq \frac{1}{f + \frac{1-f}{n}} \iff \lim_{n \to \infty} S(n) = \frac{1}{f}
$$
Si se tiene un for para $n$ hebras, donde cada una suma dos vectores $a$ y $b$ en uno final $c$. 
Podemos asumir que crear las regiones en memoria y hacer las asignaciones iniciales prueba ser una sección serial, si no falla, se pude asumir que todo es paralelizabe, por ende $f=0$.

(b) Si el tiempo de ejecución secuencial es $T_s = 1.0$ segundo para $N = 10^6$ elementos, y
el overhead de creación de región paralela es $O = 0,001$ segundos, calcule el speedup
real para $2, 4, 8$ y $16$ hilos.

$$
S(n) = \frac{1}{\frac{1}{n} + On}
$$

- $p = 2$**:** $T(2) = 0,5 + 0,001 = 0,501 \text{ s} \implies S(2) = \frac{1,0}{0,501} \approx \mathbf{1,996}$
- $p = 4$**:** $T(4) = 0,25 + 0,001 = 0,251 \text{ s} \implies S(4) = \frac{1,0}{0,251} \approx \mathbf{3,984}$
- $p = 8$**:** $T(8) = 0,125 + 0,001 = 0,126 \text{ s} \implies S(8) = \frac{1,0}{0,126} \approx \mathbf{7,936}$
- $p = 16$**:** $T(16) = 0,0625 + 0,001 = 0,0635 \text{ s} \implies S(16) = \frac{1,0}{0,0635} \approx \mathbf{15,748}$

(c) ¿Por qué el speedup real puede ser menor que el teórico de Amdahl? Identifique al
menos tres factores.
El speedup real puede ser menor por varios factores:
- Overhead no definido
- No se toma en cuenta el tamaño del problema cómo con Gustafson
- Posibles secciones críticas/manejo de concurrencia

(d) Si el tamaño del vector aumenta a $N = 10^8$ ¿cómo cambia la fracción secuencial?
¿Qué ley (Amdahl o Gustafson) es más apropiada para analizar este caso?

En este caso, si el tamaño de problema aumenta, la fracción secuencial debería aumentar, si asumimos que la creación de variables/almacenamiento, cosas del compilador, etc... 
Igual, asumiendo que OpenMP escala el tamaño del problema, osea, asumiendo weak-scaling, la ley de Gustafson debería ser la adecuada para modelar el speedup real del problema.

### Ejercicio 11. Análisis de Código: Problemas de Paralelización

Analice el siguiente código y responda:
```c++
double sum = 0.0;
# pragma omp parallel for
for (int i = 0; i < n; i++) {
	sum += array[i];
}
```
*(a) ¿Qué problema tiene este código? Explique el concepto de race condition.*

Primero, una condición de carrera es cuando dos o más escribe escriben a un recurso compartido, osea, cuando dos o más hebras entran a una sección crítica.
¿Por qué es un problema? La linea `sum += array[i]` no es una operación atómica, por ende puede ocurrir que una sobreescriba la variable compartida `sum` causando una distorsión en el valor final distinto al esperado con una versión serial.

*(b) ¿Cuál sería el speedup esperado si este código se ejecutara sin corregir el problema?*
*Justifique.*
Si asumimos overhead mínimo por creación de hebras, o incluso negligible, idem a ejercicio 10.

(c) Proponga dos soluciones diferentes usando OpenMP y explique cómo cada una afecta
el overhead y, por tanto, el speedup.


(d) Si el overhead de sincronización de una solución es $O_1 = 0,01 \cdot n$ y de la otra es
$O_2 = 0,05 \cdot \log_2(n)$, calcule el speedup para cada solución con $n = 8$ hilos y $N = 10^6$ elementos (tiempo secuencial = $1.0[s]$).


### Ejercicio 13
Analice el siguiente código OpenMP para el producto matriz-vector y = Ax:
```c++
void matrix_vector_mult(double **A, double *x, double *y, int m, int n) {
	# pragma omp parallel for schedule(static)
	for (int i = 0; i < m; i++) {
		y[i] = 0.0;
		for (int j = 0; j < n; j++) {
			y[i] += A[i][j] * x[j];
	}
}
```
*(a) Identifique qué bucle está paralelizado y explique por qué esta es una buena estrategia.*
El bucle paralelizado cómo tal es el externo, que se encarga de inicializar el arreglo resultante, por ende, siguiendo la regla de multiplicación de matriz vector
$$
y_m = A_{m\times n} \times x_n = \begin{pmatrix} A & B \\ C & D \\ E & F \end{pmatrix} \times \begin{pmatrix} G  \\ H \end{pmatrix} = \begin{pmatrix}
A \times G + B \times H \\
C \times G + D \times H \\
E \times G + F \times H \\
\end{pmatrix}
$$
En este caso, cada una de las hebras es una componente del vector resultante final, por ende, cada hebra se encarga de hacer las multiplicaciones y sumas resultantes.
**¿Por que es buena la solución?**: 
- Como son operaciones conmutativas (suma y multiplicación), no hay condición de carrera
- Si bien es una operación $O(n \times m)$, se está paralelizando el $O(m)$, minimizando overhead de creación, a diferencia si es paralelizara el loop interno.

*(b) Si el tiempo secuencial es $T_s(m, n) = 2mn$ microsegundos, calcule el speedup teórico*
*según Amdahl para una matriz $1000 \times 1000$ con $8$ hilos (asuma fracción secuencial*
*despreciable).*
Usando de nuevo la misma fórmula
$$
S(p) = \frac{T_s}{T_p(p)} \iff S(8) = \frac{2 \times 10^6}{}
$$

(c) Considere que el overhead de creación de región paralela es $O = 100$ microsegundos.
Calcule el speedup real para matrices de tamaño $100×100$, $1000×1000$ y $10000×10000$
con $8$ hilos.


(d) Analice cómo el tamaño de la matriz afecta el speedup. ¿Qué ley (Amdahl o Gustafson)
describe mejor este comportamiento? Justifique.


(e) Si cambiáramos a schedule(dynamic), ¿cómo afectaría esto al overhead y al speedup?
Explique.

