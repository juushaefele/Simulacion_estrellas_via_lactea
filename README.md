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

$$\alpha =
\begin{cases}
1.3, & 0.08 < M/M_\odot < 0.5 \\
2.3, & M/M_\odot \ge 0.5
\end{cases}$$

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
$$M_{\rm WD} = a + b\, M_{\rm ini}$$

y está basada en observaciones de cúmulos abiertos.

---

###  4.2. Estrellas de Neutrones (NS) utilizando Raithel et al. 2018

Para estrellas con masas iniciales  entre 8 y 30 $$M_\odot$$.

---

### 4.3. Agujeros Negros (BH) utilizando también Raithel et al. 2018

Para masas iniciales mayores a 30 o 35 masas solares:

Las ecuaciones entre (1) y (4), cosideran el parámetro $$f_{\rm ej}$$ que representa la fracción del material expulsado en la explosión.

$$
f_{\rm ej} = 0.9
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
