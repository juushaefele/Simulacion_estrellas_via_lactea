# Simulación Montecarlo para las estrellas de la Vía Láctea
### Autoras: **Justine Haefele & Catalina Montecino**  

---

Este proyecto implementa una simulación Monte Carlo para modelar la población estelar de la Vía Láctea.  
Dado que la galaxia contiene entre 100 y 400 mil millones de estrellas, es imposible simular cada una con códigos de evolución estelar detallados como MESA o SSE.

Por ello, realizamos una **población sintética** utilizando funciones empíricas:

- **IMF de Kroupa**
- **Tasa de Formación Estelar (SFR) constante**
- **Tiempo en secuencia principal**
- **Relaciones Masa Inicial → Masa Final (IFMR)** para  
  **enanas blancas (WD), estrellas de neutrones (NS) y agujeros negros (BH)**

El objetivo es estimar:

- La **fracción** de MS, WD, NS y BH en la simulación  
- La masa y edad del objeto **más viejo y más joven** en cada categoría.  

---

La simulación considera los siguientes elementos de evolución estelar:

---

## 1. Initial Mass Function — Kroupa (2001)

La **Initial Mass Function (IMF)** describe la distribución de masas con las que nacen las estrellas:

$$
\xi(M) \propto M^{-\alpha}
$$

La IMF de Kroupa es una ley de potencias por tramos:

$$
\alpha =
\begin{cases}
1.3, & 0.08 < M/M_\odot < 0.5 \\
2.3, & M/M_\odot \ge 0.5
\end{cases}
$$

La mayoría de las estrellas nacen con baja masa, lo que conduce a abundancias elevadas de estrellas en la secuencia principal y enanas blancas, mientras que estrellas de neutrones y agujeros negros se forman en cantidades mucho menores.

---

## 2. Tasa de Formación Estelar (SFR)

La simulación asume una **Star Formation Rate constante** desde 0.01 hasta 10 Gyr:

$$
t_{\rm birth} \sim U(0,\,10\ \mathrm{Gyr})
$$

Esto significa que todas las estrellas tienen igual probabilidad de formarse en cualquier momento de la historia galáctica.  

---

## 3. Tiempo de Vida en Secuencia Principal

Esto permite filtrar a las estrellas en función del tiempo que pasan en la secuencia principal (fusionando hidrógeno en el núcleo).

Usamos la aproximación:

$$
t_{\rm MS} = t_\odot \left( \frac{M}{M_\odot} \right)^{-2.5}
$$

donde:

- $t_\odot = 10^{10}$ años es el tiempo de vida del Sol.

Interpretación física:

- Estrellas **masivas** consumen su combustible rápidamente → vidas cortas  
- Estrellas **de baja masa** viven muchos Gyr  
- Si `edad > t_MS` → la estrella se clasifica como **remanente**

---

## 4. Clasificación de Remanentes — IFMR

El tipo de remanente se determina usando tres relaciones masa inicial–final:

---

### 4.1. Enanas Blancas (WD) — Kalirai et al. 2008

Relación empírica:

$$
M_{\rm WD} = a + b\, M_{\rm ini}
$$

Basada en observaciones de cúmulos abiertos.

---

### 4.2. Estrellas de Neutrones (NS) — Raithel et al. 2018

Para estrellas con masas iniciales:

$$
8\,M_\odot \lesssim M_{\rm ini} \lesssim 30\,M_\odot
$$

---

### 4.3. Agujeros Negros (BH) — Raithel et al. 2018

Para masas iniciales mayores a:

$$
M_{\rm ini} \gtrsim 30{-}35\,M_\odot
$$

Se usa un parámetro clave:

$$
f_{\rm ej} = 0.9
$$

que representa la fracción de material expulsado en la explosión.

---

## 5. Flujo del Algoritmo Monte Carlo

Para un número total de $N$ estrellas:

1. Muestrear masa inicial (IMF de Kroupa)  
2. Muestrear tiempo de nacimiento (SFR constante)  
3. Calcular $t_{\rm MS}$  
4. Determinar si la estrella sigue en MS o es un remanente  
5. Aplicar IFMR para WD / NS / BH  
6. Registrar masas finales, edades y tipos  
7. Generar las figuras finales

---

# Figuras Generadas

### **1. Fracciones y Edades de las Estrellas**  

Incluye:
- Fracción de MS, WD, NS y BH  
- Edad del remanente más joven y más viejo  
- Masas iniciales asociadas

---

### **2. IFMR: Simulación vs. Teoría**  

Incluye:
- Curvas IFMR teóricas de Kalirai+2008 y Raithel+2018  
- Puntos simulados para WD, NS y BH  
- Comparación directa entre teoría y Monte Carlo

---

# Ejecución del código

### 1. Clonar el repositorio

```bash
git clone https://github.com/tu_usuario/montecarlo_stars.git
cd montecarlo_stars
