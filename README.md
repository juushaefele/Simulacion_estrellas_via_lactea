# Simulacion Montecarlo para las estrellas de la Vía Láctea
### Por: *Justine Haefele & Catalina Montecino*  

---

Este proyecto utiliza una simulación Monte Carlo para modelar la población estelar de la Vía Láctea.  
Dado que la galaxia contiene entre 100 y 400 mil millones de estrellas, es imposible simular cada una con códigos de evolución estelar detallados como MESA o SSE.

Por ello, realizamos una *población sintética* utilizando funciones conocidas en la literatura:

- *IMF de Kroupa*
- *Tasa de Formación Estelar (SFR) constante*
- *Tiempo en secuencia principal*
- *Relaciones Masa Inicial  y Masa Final (IFMR)* para  
  *enanas blancas (WD), estrellas de neutrones (NS) y agujeros negros (BH)*

El objetivo es estimar:

- La *fracción* de MS, WD, NS y BH en la simulación  
- La masa y edad del objeto *más viejo y más joven* en cada categoría.  

---

La simulación considera los siguientes elementospara la evolución estelar:

## 1. Initial mass function, utilizando Kroupa (2001)

La *Initial Mass Function (IMF)* describe la distribución de masas con las que nacen las estrellas:

$$\xi(M) \propto M^{-\alpha}$$

La IMF de Kroupa es una ley de potencias que se encuentra definida por tramos:

$$
\mathrm{IMF}(M)=
\begin{cases}
M^{-1.3}, & 0.08 \le M < 0.5, \\
0.5 \, M^{-2.3}, & M \ge 0.5 .
\end{cases}
$$

La mayoría de las estrellas nacen con una baja masa, lo que conduce a una gran ccantidad de estrellas en la secuencia principal y enanas blancas, mientras que estrellas de neutrones y agujeros negros se forman en cantidades mucho menores.

---

## 2. Tasa de Formación Estelar (SFR)

La simulación asume una *Star Formation Rate constante* desde 0.01 hasta 10 Gyr. Esto significa que todas las estrellas tienen igual probabilidad de formarse en cualquier momento en la historia galáctica.  

---

## 3. Tiempo de Vida en Secuencia Principal

Permite filtrar a las estrellas en función del tiempo que viven en la secuencia principal, es decir, fusionando hidrógeno en el núcleo. 
Usamos la aproximación:

$$t_{\rm MS} = t_\odot \left( \frac{M}{M_\odot} \right)^{-2.5}$$

donde  $$t_\odot = 10^{10}$$ años es el tiempo de vida del Sol.

- Estrellas *masivas* consumen su combustible más rápidamente, por lo que tienen vidas que suelen ser más cortas.  
- Estrellas *de baja masa* viven muchos Gyr.  
- Si $$\text{edad}$$ es mayor a $$ t_{\rm MS} $$, la estrella se clasifica como un remanente.

---

## 4. Clasificación de Remanentes usando la IFMR

El tipo de remanente se determina usando las siguientes relaciones masa inicial y final:

---

### 4.1. Enanas Blancas (WD) utilizando Kalirai et al. 2008

Viene dada por:
$$
M_{\rm WD} = 0.109\, M_{\rm ini} + 0.394.
$$


y está basada en observaciones de cúmulos abiertos.

---

###  4.2. Estrellas de Neutrones (NS) utilizando Raithel et al. 2018

Para estrellas con masas iniciales  entre 8 y 30 $$M_\odot$$.

### Tramo 1:  
$$
9 \le M_{\rm ini} \le 13
$$

$$
M_{\rm NS} =
2.24
+0.508\,(M_{\rm ini}-14.75)
+0.125\,(M_{\rm ini}-14.75)^2
+0.0110\,(M_{\rm ini}-14.75)^3 .
$$

---

### Tramo 2:  
$$
13 < M_{\rm ini} < 15
$$

$$
M_{\rm NS} = 0.123 + 0.112\, M_{\rm ini}.
$$

---

### Tramo 3:  
$$
15 \le M_{\rm ini} < 17.8
$$

$$
M_{\rm NS} = 0.996 + 0.0384\, M_{\rm ini}.
$$

---

### Tramo 4:  
$$
17.8 < M_{\rm ini} < 18.5
$$

$$
M_{\rm NS} = -0.020 + 0.10\, M_{\rm ini}.
$$



---

### 4.3. Agujeros Negros (BH) utilizando también Raithel et al. 2018

Para masas iniciales mayores a 30 o 35 masas solares:

El código considera dos ramas y una interpolación:

---

### **Branch I (núcleo de He, fe j = 1):**

Para:

$$
15 \le M_{\rm ini} \le 40
$$

$$
M_{\rm BH, core} = -2.049 + 0.4140\, M_{\rm ini}.
$$

---

### **Branch II (colapso total del núcleo de CO):**

Para:

$$
45 \le M_{\rm ini} \le 120
$$

$$
M_{\rm BH, core}
=
5.697
+7.8598\times 10^{8}\, M_{\rm ini}^{-4.858}.
$$

---

### **Interpolación lineal (40–45)**

Entre los valores:

$$
(40,\,-2.049 + 0.4140 \cdot 40)
$$

y

$$
(45,\ 5.697 + 7.8598\times10^{8} \cdot 45^{-4.858})
$$

---

## 4.5 Colapso total (fej = 0)

Implementado como:

$$
M_{\rm BH,all} =
15.52
-0.3294\,(M_{\rm ini}-25.97)
-0.02121\,(M_{\rm ini}-25.97)^2
+0.003120\,(M_{\rm ini}-25.97)^3.
$$


Las ecuaciones consideran el parámetro $$f_{\rm ej}$$ que representa la fracción del material expulsado en la explosión.

$$
f_{\rm ej} = 0.9
$$

## 4.3.3 IFMR estándar (combinación núcleo + total)

$$
M_{\rm BH} =
f_{\rm ej}\, M_{\rm BH, core}
+
(1-f_{\rm ej})\, M_{\rm BH, all}.
$$

---

## 5. Monte Carlo

Para un número total de \(N\) estrellas:

1. Se muestrea la masa inicial (IMF de Kroupa)  
2. Muestrea tiempo de nacimiento (SFR constante)  
3. Calcula $$t_{\rm MS}$$  
4. Determina si la estrella sigue en MS o es un remanente  
5. Aplica IFMR para WD,  NS o BH  
6. Registra masas finales, edades y tipos  
7. Genera las figuras finales

---

# Figuras Generadas

### *1. Fracciones y Edades de las Estrellas*  

Incluye la fracción de MS, WD, NS y BH, además de la edad del remanente más joven y más viejo con sus masas iniciales asociadas

---

### *2. IFMR*  

Incluye IFMR teóricas de Kalirai+2008 y Raithel+2018 , puntos simulados para WD, NS y BH y una comparación entre teoría y Monte Carlo


# Ejecución 
### 1. Clonar el repositorio
```bash
git clone https://github.com/tu_usuario/montecarlo_stars.git
cd montecarlo_stars

Los paquetes necesarios son:

- numpy
- matplotlib
- pandas
- scipy -> stats
- scipy.interpolate -> interp1d
- scipy.stats -> binned_statistic
