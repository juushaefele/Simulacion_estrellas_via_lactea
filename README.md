# Simulacion Montecarlo para las estrellas de la Vía Láctea
### Por: *Justine Haefele & Catalina Montecino*  

---

Este proyecto utiliza una simulación Monte Carlo para modelar la población estelar de la Vía Láctea.  Dado que la galaxia contiene entre 100 y 400 mil millones de estrellas, es imposible simular cada una con códigos de evolución estelar detallados como MESA o SSE. Por ello, realizamos una población sintética utilizando funciones de evolución estelar de la literatura:


- Initial mass function (IMF) de Kroupa 2001*: esto describe la distribución de masas con las que nacen las estrellas:

$$\xi(M) \propto M^{-\alpha}$$

Es una ley de potencias que se encuentra definida por tramos:

$$
\mathrm{IMF}(M) =
\begin{cases}
M^{-1.3}, & 0.08 \le M < 0.5, \\
0.5 \, M^{-2.3}, & 0.5 \le M < 1, \\
0.5 \, M^{-2.3}, & M \ge 1.
\end{cases}
$$

La mayoría de las estrellas nacen con una baja masa, lo que conduce a una gran ccantidad de estrellas en la secuencia principal y enanas blancas, mientras que estrellas de neutrones y agujeros negros se forman en cantidades mucho menores.


- *Tasa de Formación Estelar (SFR) constante*: La simulación asume una star formation rate constante desde 0.01 hasta 10 Gyr. Esto significa que todas las estrellas tienen igual probabilidad de formarse en cualquier momento en la historia galáctica.  

- *Tiempo de vida en la secuencia principal*: El tiempo que una estrella pasa fusionando hidrógeno en el núcleo permite saber si se encuentra en la secuencia principal. Si la edad de la estrella es mayor a este tiempo, significa que es un remanente estelar. Usamos la aproximación:

$$t_{\rm MS} = t_\odot \left( \frac{M}{M_\odot} \right)^{-2.5}$$

donde  $$t_\odot = 10^{10}$$ años es el tiempo de vida del Sol.

- *Clasificación de remanentes estelares de acuerdo a la masa*: 

Sabiendo que las estrellas son remanentes, debemos saber a qué tipo corresponde, y para esto se separaron de acuerdo al rango de masa de la estrella progenitora, de la forma:

- Enanas Blancas (WD):  
  $$M_{\rm ini} \le 8$$

- Estrellas de Neutrones (NS):  
  $$9 \le M_{\rm ini} < 15$$

- Agujeros Negros (BH):  
  $$M_{\rm ini} > 18.5$$

En los rangos de masa que se sobreponen se utilizó interpolación lineal.

- *IFMR para cada tipo de remanente*: 

Para las enanas blancas (WD) se utiliza Kalirai et al. 2008, la cual viene dada por:

$$M_{\rm WD} = 0.109\ M_{\rm ini} + 0.394$$

y está basada en observaciones de cúmulos abiertos.

Para las estrellas de neutrones (NS) se usa Raithel et al. 2018, la cual está definida por tramos:

### Tramo 1:  
$$
9 \le M_{\rm ini} \le 13
$$

$$
M_{\rm NS} =
2.24
+0.508\(M_{\rm ini}-14.75)
+0.125\(M_{\rm ini}-14.75)^2
+0.0110\(M_{\rm ini}-14.75)^3 
$$

---

### Tramo 2:  
$$
13 < M_{\rm ini} < 15
$$

$$
M_{\rm NS} = 0.123 + 0.112\ M_{\rm ini}
$$

---

### Tramo 3:  
$$
15 \le M_{\rm ini} < 17.8
$$

$$
M_{\rm NS} = 0.996 + 0.0384\ M_{\rm ini}
$$

---

### Tramo 4:  
$$
17.8 < M_{\rm ini} < 18.5
$$

$$
M_{\rm NS} = -0.020 + 0.10\ M_{\rm ini}
$$

---

Para el caso de los agujeros Negros (BH) se utilizan nuevamente las expresiones de Raithel et al. 2018. El código considera dos ramas, basadas en la fracción masa que expulsa la estrella al momento de explotar como supernova, y una interpolación lineal entre 40 y 45 masas solares, ya que las ecuaciones no son continuas, así que se efectúa este procedimiento para obtener una transición que sea más suave. En el presente código consideramos una $$f_{\rm ej}=0.9$$.

---

### **Branch I (núcleo de He, fe j = 1):**

Para:

$$
15 \le M_{\rm ini} \le 40
$$

$$
M_{\rm BH, core} = -2.049 + 0.4140\ M_{\rm ini}
$$

---

### **Branch II (colapso total del núcleo de CO):**

Para:

$$45 \le M_{\rm ini} \le 120$$

$$M_{\rm BH, core}=5.697+7.8598\times 10^{8}\ M_{\rm ini}^{-4.858}$$

---

-* Colapso total en fej = 0

$$
M_{\rm BH,all} =
15.52
-0.3294\(M_{\rm ini}-25.97)
-0.02121\(M_{\rm ini}-25.97)^2
+0.003120\(M_{\rm ini}-25.97)^3
$$


-* IFMR para core y núcleo 
$$M_{\rm BH} =f_{\rm ej}\ M_{\rm BH core}+(1-f_{\rm ej})\ M_{\rm BH all}$$

---

Considerando estos ingredientes para el método de montecarlo, el objetivo es estimar la fracción de MS, WD, NS y BH en la simulación, además de la masa y edad del objeto más viejo y más joven en cada categoría.  

### *Estructura de la simulación*

Para un número total de N estrellas, pedidas al usuario:

1. Se generan masas iniciales aleatorias que siguen la distribución de la IMF de Kroupa.  
2. A cada estrella se le asigna tiempo de nacimiento, considerando una SFR constante, entre 0.01 y 10 Gyr.
3. En función del $$t_{\rm MS}$$ se determina si la estrella sigue en MS o es un remanente.
4. Los remanentes se clasifican de acuerdo a la masa, y se aplican las IFMR correspondientes para WD, NS y BH.
5. Se generan las figuras finales

—

## Los paquetes necesarios son:

- numpy
- matplotlib
- pandas
- scipy, stats
- scipy.interpolate, interp1d
- scipy.stats, binned_statistic

## Funciones principales

- `generar_masas(N)`: genera masas ZAMS y edades.  
- `clasificar_remanentes(lista_masas, ages)`: separa MS, WD, NS, BH de acuerdo a su tiempo de vida en la main sequence 
- `masa_final_wds()`, `masa_final_ns()`, `masa_bh_core()`, `masa_bh_all()`, `masa_bh_estandar()`: relaciones masa inicial–final para los remanentes. 
- `edades_extremas()`: edades mínima y máxima por tipo.  
- `graficar_fracciones_y_edades()`, `graficar_subplots_inicial_final_color_edad()`, `graficar_ifmr_ns_bh()`: funciones de graficado.  
- `main()`: corre la simulación y entrega los resultados como 2 figuras, incluyendo una opcional. 

El programa solicita una semilla y el número de estrellas a simular, luego ejecuta toda la cadena: generación, clasificación, IFMR y figuras.

# Figuras Generadas

- Histograma de fracciones: Incluye la fracción de MS, WD, NS y BH, además de la edad de la estrella de secuencia principal más joven y más vieja con sus masas iniciales asociadas.  
- IFMR para WD, NS y BH: Incluye los puntos simulados estos remanentes, señalando el remanente más joven y viejo de cada tipo.  
- (Opcional) IFMR para NS y BH: Considera las IFMR, reproduciendo la figura 1 de Raithel+2018, para las curvas teóricas, pero además muestra nuestros datos simulados.


# Referencias
- Kroupa (2001), IMF  
- Kalirai et al. (2008), IFMR WD  
- Raithel, Özel & Psaltis (2018), Modelos de NS/BH






