# Propuesta reformulada: trancón fantasma en vía circular

**Autor:** Iker Barbosa · **Fecha:** 16 de noviembre de 2026

## 1. Datos del proyecto

Esta versión reformula el anexo aprobado: cambia el método de validación y el equipo, y la sección 7 explica por qué.

- Curso: Física A: Hardware e Información, Universidad de los Andes (profesor Julián Rincón).
- Autores: Iker Barbosa (202424135) y [nombre y código del compañero].
- Entregable: proyecto final (15 % de la nota), notebook en Python con sustentación en la semana 16.
- Tema: trancón fantasma en una vía circular, sin accidente ni obstáculo.

## 2. Pregunta de investigación

¿A partir de qué densidad de carros la reducción de velocidad de un solo carro se amplifica hasta formar un trancón en una vía circular, sin accidente ni obstáculo? La pregunta no cambia respecto a la propuesta aprobada.

## 3. Modelo físico

Las ecuaciones son las del anexo aprobado. Resultan ser el modelo de velocidad óptima de Bando et al. (1995), con una velocidad deseada lineal por tramos, y por eso el criterio de inestabilidad se puede citar además de derivar.

$N$ carros de largo $\ell$ en una vía circular de longitud $L$ (densidad $N/L$). El carro $i$ tiene posición $x_i$ y velocidad $v_i$. La distancia libre al carro de adelante es (el carro $N$ sigue al carro 1):

$$s_i = x_{i+1} - x_i - \ell, \qquad s_N = x_1 + L - x_N - \ell$$

Para conservar una distancia mínima $s_0$ viajando a velocidad $v$, hace falta $s \ge s_0 + vT$, donde $T$ es el tiempo de separación deseado entre carros. Con un tope $v_{max}$, la velocidad que el conductor busca es:

$$V(s)=\begin{cases}0 & s\le s_0\\[2pt] \dfrac{s-s_0}{T} & s_0<s<s_0+v_{max}T\\[4pt] v_{max} & s\ge s_0+v_{max}T\end{cases}$$

El conductor aplica una fuerza (motor o freno) proporcional a la diferencia entre la velocidad deseada y la real. Con $m\,a_i = F_i$ y $F_i = m\,(V(s_i) - v_i)/\tau$:

$$a_i = \frac{V(s_i)-v_i}{\tau}$$

Es la misma estructura de la caída con arrastre lineal, con una velocidad terminal que depende de la distancia al carro de adelante. Suposiciones: un carril, sin adelantamientos, conductores y carros idénticos, sin ruido.

**Predicciones analíticas.** El flujo uniforme es solución exacta, con $s_e = L/N - \ell$ y $v_e = V(s_e)$. Si $s_e \ge s_0 + v_{max}T$ los carros van a $v_{max}$ sin depender del de adelante y la perturbación se disipa. En el caso contrario, la densidad supera el valor crítico:

$$\rho_c = \frac{1}{\ell + s_0 + v_{max}T}$$

Por encima de $\rho_c$, la perturbación de un carro crece si $2V'(s_e) > 1/\tau$. En el tramo lineal $V' = 1/T$, así que la condición es:

$$\tau > \frac{T}{2}$$

Es el criterio publicado $V' > a/2$ con $a = 1/\tau$ (Nakayama et al. 2016, ec. 2). Lo usamos como referencia y lo verificamos con la simulación.

## 4. Método numérico

Implementación propia en Python con NumPy y Matplotlib, usando Euler explícito como en la cinemática por pasos vista en el curso:

$$a_i^n = \frac{V(s_i^n)-v_i^n}{\tau}, \qquad v_i^{n+1} = v_i^n + a_i^n\,\Delta t, \qquad x_i^{n+1} = x_i^n + v_i^{n+1}\,\Delta t$$

Condición inicial: $x_i(0) = (i-1)\,L/N$, $v_i(0) = v_e$ para $i \ne 1$ y $v_1(0) = v_e - \delta$, con $\delta = 0{,}5$ m/s. El experimento se repite variando $N$ (la densidad) entre 10 y 40 carros.

La convergencia se verifica con una sola comparación: la misma simulación con $\Delta t = 0{,}02$ s y con $2\Delta t$, y se revisa que la amplitud del trancón y el $N$ crítico no cambien de forma apreciable. Esta verificación reemplaza a la comparación con RK4 del anexo aprobado (sección 7).

Control de calidad en cada corrida: la distancia libre mínima debe ser positiva. Si una combinación de parámetros produce choques (distancia negativa), se reporta como límite del modelo y no se usa para conclusiones.

## 5. Parámetros y fuentes

Todos los parámetros salen de experimentos publicados en pistas circulares, salvo los dos que son decisión nuestra ($\delta$ y $\Delta t$). El único que ajustamos es $\tau$, y por eso se reporta como barrido y no como valor único.

| Parámetro | Valor | Fuente |
|:------------------------|:---------------|:------------------------------------------|
| Longitud de la pista $L$ | 314 m | Tadaki et al. 2013 |
| Largo del carro $\ell$ | 3,885 m | Tadaki et al. 2013 (Toyota Vitz) |
| Distancia mínima $s_0$ | 2 m | Nakayama et al. 2016 (separación mínima $\approx$ 6 m = largo + 2 m) |
| Velocidad máxima $v_{max}$ | 11,1 m/s (40 km/h) | Tadaki et al. 2013 (flujo libre $\approx$ 40 km/h) |
| Tiempo de separación $T$ | 1 s | Nakayama et al. 2016, ec. 4 (6 m / 6 m/s) |
| Tiempo de respuesta $\tau$ | 0,5 / 0,7 / 1,0 s | Barrido. El valor publicado ($\approx$ 0,56 s) está casi en el umbral $T/2$ |
| Perturbación inicial $\delta$ | 0,5 m/s | Decisión nuestra |
| Paso de tiempo $\Delta t$ | 0,02 s | Decisión nuestra, verificada con $2\Delta t$ |
| $N$ de carros | 10 a 40 | Rango de Tadaki et al. 2013 |

Valor de trabajo: $\tau = 0{,}7$ s. Con $\tau = 0{,}5$ s el flujo es estable ($\tau$ no supera $T/2$), con $0{,}7$ s se forma el trancón sin choques (distancia mínima $\approx$ 0,5 m), y desde $0{,}8$ s aparecen distancias negativas, que se reportan como límite del modelo.

## 6. Validación

El modelo se valida contra experimentos publicados, no contra datos propios. Los números del modelo salen de simulaciones de 1500 s con los parámetros de la sección 5; las discrepancias se discutirán en el notebook como limitaciones.

| Comparación | Modelo | Publicado | Lectura |
|:--------------------|:----------------|:----------------|:-------------------------------|
| $N$ crítico en 314 m | 18–19 (analítico 18,5) | 25–28 (Tadaki et al. 2013) | El modelo subestima $\approx$ 30 %; el orden de magnitud es correcto |
| Densidad crítica en autopista ($v_{max}$ = 120 km/h, $T$ = 1 s) | 0,0255 m$^{-1}$ | $\approx$ 0,025 m$^{-1}$ (25 veh/km) | Coincide |
| Velocidad del frente del trancón ($N$ = 25–34, $\tau$ = 0,7 s) | $\approx$ 12,5 km/h hacia atrás | $\approx$ 20 km/h (Tadaki 2013, Sugiyama 2008) | El modelo es $\approx$ 35 % más lento |
| $N$ = 40 en 314 m | Predice trancón | Flujo homogéneo (Tadaki 2013) | Falla que también reportan Nakayama et al. 2016 para este modelo |
| $N$ = 22 en 230 m | $N$ crítico analítico 13,5, se espera trancón | Hay trancón (Sugiyama 2008) | Por verificar con la simulación |

Limitación adicional: con $N$ = 25–34 el modelo forma 2 o 3 trancones a la vez, mientras que los experimentos muestran uno solo. Se presenta como discrepancia, sin atribuirle una causa que no hayamos verificado.

## 7. Cambios respecto a la propuesta aprobada y por qué

Cambiamos la fuente de calibración y la validación porque los datos de ARED no permiten identificar $T$ ni $s_0$, y los experimentos publicados sí fijan parámetros y resultados comparables. La pregunta, las ecuaciones del modelo, el método de Euler, la condición inicial y el barrido de $N$ no cambian.

| Aspecto | Antes | Ahora | Por qué |
|:----------------|:--------------------|:--------------------|:----------------------------------|
| Fuente de $T$ y $s_0$ | Calibrar con datos de ARED (exp. A) | Valores de experimentos circulares publicados | El ajuste de $v$ contra $s$ con ARED dio $T \approx$ 10 s y $s_0 \approx -23$ m (sin sentido físico), con $r$ = 0,37; la regresión de la aceleración dio $R^2$ = 0,03 |
| Validación | Sin comparación externa | Contra Tadaki 2013, Sugiyama 2008 y Nakayama 2016 (sección 6) | La guía pide validar; estos experimentos tienen la misma geometría (pista circular, sin obstáculo) |
| Verificación numérica | Euler con RK4 como comparación | Euler con $\Delta t$ y $2\Delta t$ | Verifica la misma convergencia con menos código y sin un segundo integrador que defender |
| Nombre de $T$ | Tiempo de reacción | Tiempo de separación deseado | La derivación es la misma ($s \ge s_0 + vT$); el nombre coincide con el uso en la literatura ($T$ = 6 m / 6 m/s $\approx$ 1 s) y no se confunde con $\tau$, el tiempo de respuesta |
| Criterio $\tau > T/2$ | Derivado por linealización | Derivado y citado | Es el criterio $V' > a/2$ publicado (Bando 1995; Nakayama 2016, ec. 2) y se verifica en la simulación |
| Autores | Tres integrantes | Dos integrantes | El grupo quedó de dos personas |

Recomendamos esta ruta porque cada número del modelo tiene una fuente que se puede señalar, y porque las discrepancias que aparecen (sección 6) son limitaciones del modelo de velocidad óptima ya reportadas, no errores de calibración propios.

## 8. Declaración de uso de IA generativa

Usamos Claude (Anthropic, modelo Claude Sonnet 5.5, en un proyecto de claude.ai) como asistente de análisis, código exploratorio y redacción. Las decisiones del proyecto y la responsabilidad por el contenido son de los autores.

**Para qué se usó:**

- Leer los tres artículos de la sección 5 y extraer parámetros y resultados publicados (los artículos los aportamos nosotros).
- Analizar el archivo exp. A de ARED: ajuste de $v$ contra $s$ y regresión de la aceleración, que llevó a descartar ARED.
- Escribir y ejecutar simulaciones exploratorias en Python: barrido de $\tau$, $N$ crítico, velocidad del frente del trancón y un prototipo de visualización (anillo, diagrama espacio-tiempo, animación).
- Proponer la validación por literatura y redactar borradores de esta propuesta y de la estructura del notebook.

**Cómo se verificó:** las cifras publicadas se contrastaron con el texto completo de los artículos. El criterio $\tau > T/2$ se comprobó numéricamente ($N$ = 18 estable y $N$ = 19 inestable, frente al valor analítico 18,5). Esa verificación corrigió dos errores de la IA durante el proceso: el largo del carro y la velocidad de la onda del trancón. Las cifras de la sección 6 salen de simulaciones que ejecutó la IA; el notebook entregado las reproduce y es el que se ejecuta en vivo.

**Responsabilidad:** los autores responden por todo el contenido de esta propuesta, del notebook y del póster, sin importar qué parte haya generado la IA.

**Por completar antes de entregar:**

- [ ] Quién escribió el código inicial de la simulación (`sim2.py`) y con qué ayuda de IA.
- [ ] Si se usó IA en el póster y para qué.
- [ ] Cualquier otra herramienta usada (otro asistente, corrector, traductor).

## Referencias

- Bando M, Hasebe K, Nakayama A, Shibata A, Sugiyama Y (1995). Dynamical model of traffic congestion and numerical simulation. Phys. Rev. E 51, 1035–1042.
- Nakayama A et al. (2016). Quantitative explanation of circuit experiments and real traffic using the optimal velocity model. [New J. Phys. 18, 043040](https://doi.org/10.1088/1367-2630/18/4/043040).
- Sugiyama Y et al. (2008). Traffic jams without bottlenecks: experimental evidence for the physical mechanism of the formation of a jam. [New J. Phys. 10, 033001](https://hdl.handle.net/11094/93262).
- Tadaki S et al. (2013). Phase transition in traffic jam experiment on a circuit. [New J. Phys. 15, 103034](https://hdl.handle.net/11094/93261).
