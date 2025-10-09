# INFORME DE FÍSICA
## Datos personales:  
Nombre: Ferrufino Roman Ana Crisbel  
Grupo: B1

## DATOS:
### Fórmulas:

#### Volumen (usando diámetro)

$$
V = \frac{\pi H}{4}(D_e^2 - D_i^2)
$$

#### Derivadas parciales usadas para la propagación del error de V

$$
\frac{\partial V}{\partial D_e} = \frac{\pi H D_e}{2}, \quad
\frac{\partial V}{\partial D_i} = -\frac{\pi H D_i}{2}, \quad
\frac{\partial V}{\partial H} = \frac{\pi}{4}(D_e^2 - D_i^2)
$$

#### Suma en cuadratura 

$$
\sigma_V = \sqrt{
\left( \frac{\partial V}{\partial D_e} \sigma_{D_e} \right)^2 +
\left( \frac{\partial V}{\partial D_i} \sigma_{D_i} \right)^2 +
\left( \frac{\partial V}{\partial H} \sigma_H \right)^2
}
$$

#### Densidad y sus derivadas 

$$
\rho = \frac{m}{V}, \quad
\frac{\partial \rho}{\partial m} = \frac{1}{V}, \quad
\frac{\partial \rho}{\partial V} = -\frac{m}{V^2}
$$
  
### Ejemplo númerico (valores usados en el programa)

Valores de ejemplo en el main:

- Di = 1.0
- De = 1.2
- H = 1.5

- Di_err = 0.01
- De_err = 0.01
- H_err = 0.01

- m = 2.
- m_err = 0.05
## Código:

``` python

import math

# Volumen del cilindro hueco
def cilindro_hueco(Di, De, H):
    return (math.pi * H / 4) * (De**2 - Di**2)

# Error por derivadas
def cilindro_hueco_err_deriv(Di, De, H, Di_err, De_err, H_err):
    Di_contrib = (math.pi * H / 2) * Di * Di_err
    De_contrib = (math.pi * H / 2) * De * De_err
    H_contrib = (math.pi / 4) * (De**2 - Di**2) * H_err
    return math.sqrt(Di_contrib**2 + De_contrib**2 + H_contrib**2)

# Error por diferencias finitas
def cilindro_hueco_err_diff(Di, De, H, Di_err, De_err, H_err):
    Di_contrib = abs(cilindro_hueco(Di + Di_err, De, H) - cilindro_hueco(Di - Di_err, De, H)) / 2
    De_contrib = abs(cilindro_hueco(Di, De + De_err, H) - cilindro_hueco(Di, De - De_err, H)) / 2
    H_contrib  = abs(cilindro_hueco(Di, De, H + H_err) - cilindro_hueco(Di, De, H - H_err)) / 2
    return math.sqrt(Di_contrib**2 + De_contrib**2 + H_contrib**2)

# Densidad del cilindro hueco
def densidad(m, V):
    return m / V

# Error de densidad por derivadas
def densidad_err_deriv(m, V, m_err, V_err):
    m_contrib = (1 / V) * m_err
    V_contrib = (m / (V**2)) * V_err
    return math.sqrt(m_contrib**2 + V_contrib**2)

# Error de densidad por diferencias finitas
def densidad_err_diff(m, V, m_err, V_err):
    m_contrib = abs(densidad(m + m_err, V) - densidad(m - m_err, V)) / 2
    V_contrib = abs(densidad(m, V + V_err) - densidad(m, V - V_err)) / 2
    return math.sqrt(m_contrib**2 + V_contrib**2)

# Programa principal y Asignación de valores
def main_cilindro_hueco():
    Di = 1.0        # Diámetro interior
    De = 1.2        # Diámetro exterior
    H = 1.5         # Altura
    Di_err = 0.01   # Eror diámetro interior
    De_err = 0.01   # Error diámetro exterior
    H_err = 0.01    # Error altura
    m = 2.5         # Masa
    m_err = 0.05    # Error masa

    V = cilindro_hueco(Di, De, H)
    err_V_deriv = cilindro_hueco_err_deriv(Di, De, H, Di_err, De_err, H_err)
    err_V_diff = cilindro_hueco_err_diff(Di, De, H, Di_err, De_err, H_err)

    rho = densidad(m, V)
    err_rho_deriv = densidad_err_deriv(m, V, m_err, err_V_deriv)
    err_rho_diff = densidad_err_diff(m, V, m_err, err_V_diff)

    print("\nVolumen del cilindro hueco:", round(V, 6))
    print("Error volumen (derivadas):", round(err_V_deriv, 6))
    print("Error volumen (diferencias finitas):", round(err_V_diff, 6))

    print("\nDensidad del cilindro hueco:", round(rho, 6))
    print("Error densidad (derivadas):", round(err_rho_deriv, 6))
    print("Error densidad (diferencias finitas):", round(err_rho_diff, 6))

if __name__ == "__main__":
    main_cilindro_hueco()

``` 
## MODO DE USO DEL PROGRAMA


#### Primera línea de código:
- Importa la librería math para usar funciones matemáticas como pi y sqrt().
![Cabecera del script](imagenes/01_línea.jpg)
*Figura 1. Cabecera del archivo y dependencias.*

##### Volumen:
- Define funciones

![Función volumen](imagenes/02_volumen.jpg)
*Figura 2. Cálculo del volumen \(V = \pi H/4 (D_e^2 - D_i^2)\).*

- Calcula el volumen de un cilindrohueco mediante la fórmula  
$$
V = \frac{\pi H}{4}(D_e^2 - D_i^2)
$$  
- Usa diámetro interior y exterior con la altura
- Devuelve el valor en unidades cúbicas

#### Propagación por derivadas:
![Error por derivadas](imagenes/03_error_derivadas.jpg)
*Figura 3. Propagación analítica del error (derivadas parciales).*

- Usa derivadas parciales
- Calcula la contribución al error de cada variable: Di, De, H
- Usa fórmula general de propagación:  
$$
\sigma_V = \sqrt{
\left( \frac{\partial V}{\partial D_e} \sigma_{D_e} \right)^2 +
\left( \frac{\partial V}{\partial D_i} \sigma_{D_i} \right)^2 +
\left( \frac{\partial V}{\partial H} \sigma_H \right)^2
}
$$

#### Diferencias finitas
![Error diferencias finitas](imagenes/04_error_diff.jpg)
*Figura 4. Propagación numérica del error por diferencias centradas.*

- Aplica método numérico de diferencias finitascentrales
- Evalúa la diferencia entre: (para cada variable)
$$
\Delta V = V(D + \Delta D) - V(D - \Delta D)
$$ 
- Devuelve una estimación del error


#### Densidad y sus errores
![Funciones densidad](imagenes/05_densidad.jpg)
*Figura 5. Cálculo de la densidad y propagación de su error.*

- Calcula la densidad del cilindro hueco con la relación 
$$
\rho = \frac{m}{V}
$$
- Implementa la propagación del error en la densidad tanto por derivadas como por diferencias finitas.
- Usa los errores de masa y volumen 

#### Datos
![06 Datos de entrada](imagenes/06_valores_main.jpg)
*Figura 6. Datos experimentales usados para el cálculo.

- Contiene el bloque main() o principal del programa.
- Define los valores experimentales: diámetros, altura, masa

#### Tabla de variables y unidades
| Variable | Significado | Valor | Unidad |
|---|---:|---:|---|
| Di | Diámetro interior | 1.00 ± 0.01 | m |
| De | Diámetro exterior | 1.20 ± 0.01 | m |
| H  | Altura | 1.50 ± 0.01 | m |
| m  | Masa | 2.50 ± 0.05 | kg |




## RESULTADO
### (Seis cifras significativas)
Volumen del cilindro hueco: 0.518363
Error volumen (derivadas): 0.036967
Error volumen (diferencias finitas): 0.036967

Densidad del cilindro hueco: 4.822877
Error densidad (derivadas): 0.357211
Error densidad (diferencias finitas): 0.358904

## NOTAS ADICIONALES

- Todas odas las medidas deben estar en el mismo sistema 

- El volumen debe ser distinto de cero 

- Equivalencias:

Di → diametro_interior

De → diametro_exterior

H → altura

Di_err → err_diametro_interior

De_err → err_diametro_exterior

H_err → err_altura

m → masa

m_err → err_masa

V → volumen

rho → densidad