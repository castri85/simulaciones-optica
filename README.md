# Z-scan: fundamento físico y simulación interactiva

[**Abrir la simulación**](https://castri85.github.io/simulaciones-optica/)

Este recurso ilustra cómo cambia la señal de un experimento Z-scan al desplazar una muestra por el foco de un haz gaussiano. Esta guía explica las ecuaciones y el cálculo de la versión publicada. Las curvas son **didácticas**: algunos pasos emplean aproximaciones y no sustituyen una propagación completa ni una calibración experimental.

## 1. La idea del experimento

Un láser atraviesa una lente, la muestra móvil y, opcionalmente, una apertura antes del detector. Al desplazar la muestra a lo largo de $z$, la intensidad que experimenta aumenta al acercarse al foco y disminuye al alejarse. La gráfica muestra la potencia detectada normalizada en función de la posición.

- **Apertura abierta:** se recoge toda la potencia transmitida; es sensible a la absorción en este modelo.
- **Apertura cerrada:** se recoge solo una fracción del haz; la señal depende de su redistribución espacial y puede incluir absorción y refracción.

Tomamos $z=0$ en el foco y $z<0$ antes del foco. La curva valle–pico corresponde aquí a $n_2>0$; pico–valle, a $n_2<0$.

## 2. Haz gaussiano y escala espacial

Con radio de cintura $w_0$, longitud de onda en vacío $\lambda$ y radio definido al nivel $1/e^2$ de intensidad:

$$z_R=\frac{\pi w_0^2}{\lambda},\qquad w(z)=w_0\sqrt{1+(z/z_R)^2}.$$

La relación anterior para $z_R$ supone propagación en vacío. Definiendo $x=z/z_R$ e $I_0=I(0,0)$:

$$I(r,z)=\frac{I_0}{1+x^2}\exp\!\left[-\frac{2r^2}{w^2(z)}\right].$$

En el eje, $I(0,z)/I_0=1/(1+x^2)$: vale 1 en el foco y 0,5 en $z=\pm z_R$. La interfaz permite variar $z_R$ entre 0,5 y 3 mm; el barrido se mantiene entre −10 y +10 mm. Aquí $z_R$ y la fuerza efectiva se varían independientemente: no es un cambio de cintura a potencia de láser fija.

## 3. Los controles y sus magnitudes efectivas

La detección, el signo de $n_2$ y el mecanismo de absorción pueden elegirse independientemente. El deslizador **Interaction strength** define un número $g$ entre 0 y 1, compartido por los mecanismos activos:

| Opción | Correspondencia de la implementación |
|---|---|
| Refracción | $\Delta\Phi_0=\operatorname{sgn}(n_2)\,0{,}5g$ rad |
| Absorción de $m$ fotones, $m=2,3,4$ | Pérdida de intensidad sobre el eje en el foco: $d=0{,}8g$ |
| Absorción lineal | $\alpha L=0{,}7g$ |
| Absorción saturable | $\alpha_0L=0{,}7$ e $I_0/I_{\rm sat}=5g$ |

El porcentaje de $g$ **no es** una potencia, una intensidad en W/cm² ni necesariamente la pérdida de potencia total. Si se activan refracción y absorción simultáneamente, el mismo deslizador cambia ambas; sus magnitudes no se ajustan de manera independiente. La intensidad y la fase son parámetros efectivos, no mediciones calibradas.

## 4. Refracción no lineal y apertura cerrada

En un medio Kerr, el índice puede escribirse $n=n_0+n_2I$. En el caso de absorción lineal homogénea, la fase sobre el eje en el foco suele expresarse como

$$\Delta\Phi_0=\frac{2\pi}{\lambda}\,n_2 I_0 L_{\rm eff},\qquad L_{\rm eff}=\frac{1-e^{-\alpha L}}{\alpha},$$

con $L_{\rm eff}\to L$ cuando $\alpha\to0$. Esta relación explica el significado de la fase; **la interfaz actual asigna directamente** $\Delta\Phi_0=\pm0{,}5g$ y no calcula $n_2$ a partir de parámetros dimensionales independientes.

Para fase pequeña, el código utiliza la relación de referencia

$$R(x)\approx 1+\frac{4\Delta\Phi_0 x}{(1+x^2)(9+x^2)}.$$

Esta forma corresponde a una aproximación clásica de apertura pequeña; en la aplicación se usa como relación efectiva con una transmisión lineal de apertura fija $S=0{,}25$. En ausencia de absorción, la curva cerrada coincide con $R(x)$. Con absorción, la aplicación combina la atenuación radial con una aceptación efectiva; no resuelve la difracción de un campo complejo.

## 5. Absorción multifotónica

La ley local para absorción de $m$ fotones es

$$\frac{dI}{d\ell}=-\beta_m I^m,\qquad m>1.$$

Para una muestra homogénea de espesor $L$, sin absorción lineal simultánea en este modo, la integración da

$$\frac{I_{\rm out}}{I_{\rm in}}=\left[1+(m-1)\beta_m L I_{\rm in}^{m-1}\right]^{-1/(m-1)}.$$

Si se fija la pérdida central focal $d$, la aplicación usa

$$q_m=(1-d)^{-(m-1)}-1=(m-1)\beta_m L I_0^{m-1}.$$

Para integrar la potencia del perfil gaussiano se introduce la coordenada acumulada de potencia $u=1-e^{-2r^2/w(z)^2}$, entre 0 y 1. De este modo $I_{\rm in}/I_0=(1-u)/(1+x^2)$ y la fracción local que sobrevive es

$$t_m(u,x)=\left[1+q_m\left(\frac{1-u}{1+x^2}\right)^{m-1}\right]^{-1/(m-1)}.$$

La potencia total transmitida con apertura abierta es $F_{\rm open}(x)=\int_0^1 t_m(u,x)\,du$. El programa evalúa esta integral numéricamente mediante Simpson adaptativo. La absorción es mayor cerca del foco, por lo que se observa un valle.

**Ejemplo:** con 2PA, $g=0{,}5$ y $z=0$, la pérdida central es $d=0{,}4$, $q_2=2/3$ y $F_{\rm open}=\ln(1+q_2)/q_2\approx0{,}766$. Se transmite aproximadamente el 76,6 % de la potencia, aunque el centro del haz pierda el 40 %, porque sus zonas externas tienen menor intensidad.

## 6. Absorción lineal y saturable

En absorción lineal, $dI/d\ell=-\alpha I$ y $t_{\rm lin}=e^{-\alpha L}$. La potencia absoluta disminuye, pero la curva de apertura abierta normalizada a la misma referencia lineal permanece en 1.

Para absorción saturable se usa

$$\frac{dI}{d\ell}=-\frac{\alpha_0 I}{1+I/I_{\rm sat}}.$$

Al integrar a través de la muestra, la transmisión local $t=I_{\rm out}/I_{\rm in}$ satisface

$$\ln t+s(t-1)=-\alpha_0L,\qquad s=I_{\rm in}/I_{\rm sat}.$$

El código resuelve esta ecuación numéricamente e integra radialmente. La transmisión absoluta sigue siendo menor o igual a 1; la transmitancia **normalizada** puede superar 1 cerca del foco porque la referencia es la transmisión a baja intensidad $e^{-\alpha_0L}$.

## 7. Cómo se obtiene la señal del detector

Con $t(u,x)$ como transmisión local, en apertura abierta el programa calcula $F_{\rm open}=\int_0^1t\,du$. Para la apertura cerrada establece la fracción efectiva aceptada $A(x)=S R(x)$ y calcula $F_{\rm closed}=\int_0^{A(x)}t\,du$. Esta asociación del perfil radial incidente con la aceptación del detector es **esquemática**, especialmente cuando absorción y refracción actúan a la vez.

La salida representada es

$$T_N(x)=\frac{F_{\rm det}(x)}{S_{\rm det}e^{-a}},$$

donde $S_{\rm det}=1$ en apertura abierta y $S_{\rm det}=S=0{,}25$ en cerrada. Para absorción lineal $a=\alpha L$; para saturable $a=\alpha_0L$; para la opción multifotónica aislada o sin absorción, $a=0$. La gráfica muestra $T_N$, mientras que los porcentajes de potencia y los puntos animados representan el flujo absoluto de manera ilustrativa.

Durante la introducción de la muestra, la animación incrementa gradualmente la visibilidad de la interacción. Las relaciones anteriores describen el estado de interacción completamente activada, como en el capítulo **Response**.

## 8. Alcance y limitaciones

- Se supone un haz gaussiano, simetría radial y una muestra suficientemente delgada para esta descripción simplificada.
- La curva refractiva usa una expresión de fase pequeña y un tratamiento efectivo de la apertura; $|\Delta\Phi_0|\leq0{,}5$ rad es el rango configurado, no una garantía universal de precisión.
- No hay propagación completa por difracción, lente térmica, acumulación térmica, respuesta temporal del pulso ni integración temporal; los dibujos y partículas ayudan a visualizar el resultado.
- El perfil, los parámetros efectivos y la normalización deben interpretarse según los supuestos anteriores; no se pretende extraer directamente $n_2$ o $\beta_m$ de un experimento sin calibración y un modelo adecuado.

## Referencias

1. M. Sheik-Bahae, A. A. Said, T.-H. Wei, D. J. Hagan y E. W. Van Stryland, “Sensitive measurement of optical nonlinearities using a single beam”, *IEEE Journal of Quantum Electronics* **26**, 760–769 (1990). [DOI: 10.1109/3.53394](https://doi.org/10.1109/3.53394).
2. [Registro de la publicación en University of Central Florida](https://stars.library.ucf.edu/scopus1990/1653/).

Las ecuaciones de esta guía se han contrastado con la implementación JavaScript publicada en este repositorio; las adaptaciones de visualización y normalización específicas de la aplicación se describen explícitamente arriba.

