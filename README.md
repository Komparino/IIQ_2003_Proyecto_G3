# IIQ_2003_Proyecto_G3 - Modelamiento de Transferencia de Calor en Paneles Fotovoltaicos para el norte de Chile

## 1. Descripción General y Contexto
Este proyecto busca estudiar la eficiencia de paneles fotovoltaicos bajo las condiciones de alta radiación del norte de Chile. Toddo esto bajo el contexto de la crisis climática y la búsqueda de energías limpias (ODS 7) y considerando que Chile posee un potencial energético privilegiaco, particularmente en el Desierto de Atacama para la energía solar.

Sin embargo, existe una problemática física: la eficiencia eléctrica de los paneles disminuye al aumentar su temperatura. Como parte de la radiación solar absorbida se disipa como calor, adquiere una especial relevancia la modelación del perfil de temperatura transitorio para predecir el comportamiento real del panel y optimizar su implementación en zonas áridas.

## 2. Descripción del Sistema Físico
El sistema modelado consiste en un panel fotovoltaico tratado como una placa sólida expuesta a condiciones ambientales variables a lo largo de un día.

* **Descripción de fenómeno:** Conducción de calor en estado transitorio unidimensional (eje $z$, espesor).
* **Condiciones de borde:**
    * **Cara Frontal ($z=0$):** Recibe radiación solar variable $G(t)$ y pierde energía por generación eléctrica.
    * **Cara Posterior ($z=Z$):** Disipa calor por convección natural hacia el ambiente según la ley de enfriamiento de Newton.

### Supuestos Adoptados
Para el modelo matemático se consideraron las siguientes simplificaciones:
1.  **Modelo 1D:** Se desprecia la conducción en los ejes $x$ e $y$ ($L, W >> Z$).
2.  **Propiedades Constantes:** Densidad ($\rho$), calor específico ($c_p$) y conductividad ($k$) se asumen constantes como un promedio de los materiales.
3.  **Términos Advectivos y Viscosos Despreciables:** El panel es un sólido rígido ($\vec{v}=0, \phi_v=0$).
4.  **Régimen Transitorio:** La temperatura varía hora a hora dependiente de la radiación solar.

## 3. Método Numérico
Para resolver la anterior, se utilizó el Método de Diferencias Finitas con un esquema explícito (FTCS, Forward Time Centered Space).

### Fundamento y Discretización
La ecuación gobernante $\rho c_p \frac{\partial T}{\partial t} = k \frac{\partial^2 T}{\partial z^2}$ se discretizó de la siguiente forma:

* **Nodos Internos:** Diferencia centrada en el espacio y progresiva en el tiempo.
    $$T_j^{n+1} = T_j^n + \alpha \Delta t \left( \frac{T_{j+1}^n - 2T_j^n + T_{j-1}^n}{(\Delta z)^2} \right)$$
    Donde $\alpha = k / (\rho c_p)$ es la difusividad térmica.

* **Condiciones de Borde:**
    * **Frontal ($j=0$):** Balance de flujo de calor con radiación absorbida y eficiencia eléctrica variable $\eta(T)$.
    * **Posterior ($j=N$):** Balance de flujo conductivo con convección (Ley de enfriamiento de Newton).

### Justificación y Estabilidad
Se escogió el método explícito por su eficiencia computacional para problemas 1D y facilidad de implementación. Para asegurar la convergencia, el paso de tiempo ($\Delta t$) cumple el criterio de estabilidad de Fourier:
$$\Delta t \le \frac{(\Delta z)^2}{2\alpha}$$

## 4. Instrucciones de Ejecución
El código fue desarrollado en Python. Sigue estos pasos para reproducir la simulación:

### Requisitos
Se requiere Python 3.x y las siguientes librerías:
* `numpy` (Cálculo matricial)
* `matplotlib` (Gráficos)

Puedes instalarlas ejecutando:
* 'pip install numpy matplotlib0'


### Pasos para correr la simulación
1.  Clona el repositorio o descarga los archivos.
2.  Navega a la carpeta del proyecto en tu terminal.
3.  Ejecuta el script principal (carpeta `código`):
    ```bash
    python src/main.py
    ```
4.  El programa generará los perfiles de temperatura y guardará los gráficos en la carpeta `images`.

## 5. Resultados y Discusión
La simulación de un día de verano (12 horas de sol) arrojó los siguientes resultados clave, considerando un espesor de panel de 0.004 :

### Perfil de Temperatura
![Temperatura Caras](images/temp_caras.png)

Se observa que la temperatura del panel sigue la curva de radiación solar, alcanzando un máximo cercano a 460 K (187°C) al mediodía. La diferencia de temperatura entre la cara frontal y posterior es pequeña (~4 K) debido al bajo espesor del panel y su alta difusividad térmica.

### Mapa de Calor en el Espesor
![Mapa de Calor](images/mapa_calor.png)

El mapa de calor muestra una mayor intensidad térmica en la cara frontal entre las 10:00 y 15:00 hrs. Esta acumulación de calor influye negativamente en la eficiencia eléctrica.

### Eficiencia Eléctrica
![Eficiencia Eléctrica](images/eficiencia.png)

Como se predijo teóricamente, la eficiencia eléctrica muestra un comportamiento inverso a la temperatura:
* **Eficiencia Nocturna:** ~11% (Valor nominal).
* **Eficiencia Mínima:** Cae hasta un ~2% en las horas de mayor calor (12:00 - 14:00 hrs).

**Conclusión:** El sobrecalentamiento del panel afecta negativamente su rendimiento, reduciendo la eficiencia significativamente respecto a su valor durante las horas punta. Los resultados mostrados validan la necesidad de estudiar sistemas de disipación térmica que permitan contrarrestar esta baja en la eficiencia.
