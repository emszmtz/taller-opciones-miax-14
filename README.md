# Taller de Opciones SPY - MIAX 14

**Autor:** Emilio Sánchez  
**Programa:** Master in Financial Markets (MIAX) - Edición 14  
**Activo:** SPY (SPDR S&P 500 ETF Trust)  
**Broker:** Interactive Brokers (IBKR)

---

## Descripción

Este proyecto constituye un taller práctico y completo sobre trading de opciones utilizando el ETF SPY como subyacente. Integra conexión en tiempo real con Interactive Brokers, cálculo de griegas mediante el modelo Black-Scholes, análisis de superficies de volatilidad, estrategias de cobertura delta y backtesting de estrategias sistemáticas.

El notebook está diseñado como material educativo y de aplicación práctica para el programa MIAX, combinando fundamentos teóricos con implementación en Python y datos de mercado reales.

---

## Estructura del Proyecto

### Parte 1: Conexión y Fundamentos de Opciones

| Sección | Contenido |
|---------|-----------|
| **1.1** | Conexión a Interactive Brokers desde Python usando `ib_insync` |
| **1.2** | Obtención de cadenas de opciones y datos de mercado |
| **1.3** | Cálculo de volatilidad implícita mediante bisección (Brent) |
| **1.4** | Construcción de superficies de volatilidad (smile/skew) |
| **1.5** | Cálculo de griegas: Delta, Gamma, Theta, Vega, Rho |
| **1.6** | Valoración de opciones con Black-Scholes |
| **1.7** | Simulación de delta-hedging con Monte Carlo |
| **1.8** | Funciones para envío de órdenes (simples y combos) |

### Parte 2: Estrategias

| Sección | Contenido |
|---------|-----------|
| **2.1** | Backtest de Long Straddle periódico (mensual) |
| **2.2** | Comparación: Straddle sin cobertura vs con delta-hedging |
| **2.3** | Análisis de P&L histórico y estadísticas |
| **2.4** | Ejecución eficiente: Combos vs patas sueltas |
| **2.5** | Neutralización de delta con spreads de opciones |

### Anexos

- Comparativa SPY vs SPX (americanas vs europeas)
- Implicaciones de dividendos y ejercicio anticipado
- Consideraciones de liquidez y tamaño de contrato

---

## Requisitos

### Dependencias Python

```bash
pip install ib_insync numpy pandas scipy matplotlib yfinance
```

| Librería | Uso |
|----------|-----|
| `ib_insync` | Conexión y trading con Interactive Brokers |
| `numpy` | Cálculos numéricos y vectorización |
| `pandas` | Manipulación de datos y DataFrames |
| `scipy` | Optimización (bisección para IV) y estadísticas |
| `matplotlib` | Visualizaciones y gráficos |
| `yfinance` | Datos históricos para backtesting |

### Interactive Brokers

- TWS (Trader Workstation) o IB Gateway ejecutándose
- Configuración API habilitada en TWS/Gateway
- Puerto 7497 (paper trading) o 7496 (live)
- Suscripción a datos de mercado de opciones (recomendado)

---

## Uso

### 1. Iniciar TWS o IB Gateway

Asegúrate de que TWS está corriendo con la API habilitada en `Configuración > API > Settings`.

### 2. Ejecutar el Notebook

```bash
jupyter notebook opciones_spy_ibkr_1.ipynb
```

### 3. Conexión

El notebook se conecta automáticamente al iniciar la primera celda:

```python
from ib_insync import IB
ib = IB()
ib.connect('127.0.0.1', 7497, clientId=1)  # Paper trading
```

### 4. Envío de Órdenes (con confirmación)

```python
# Orden simple de Call
trade = enviar_orden_con_confirmacion(
    ib,
    tipo_orden='simple',
    simbolo='SPY',
    strike=600,
    vencimiento='20250321',
    tipo_opcion='C',
    accion='BUY',
    cantidad=1
)

# Straddle (combo)
trade = enviar_orden_con_confirmacion(
    ib,
    tipo_orden='straddle',
    simbolo='SPY',
    strike=600,
    vencimiento='20250321',
    accion='BUY',
    cantidad=1
)
```

---

## Funciones Principales

### Valoración

```python
black_scholes(S, K, T, r, sigma, q=0, tipo='C')
```
Calcula el precio teórico de una opción europea.

### Griegas

```python
calcular_griegas(S, K, T, r, sigma, q=0, tipo='C')
```
Retorna diccionario con Delta, Gamma, Theta (diario), Vega y Rho.

### Volatilidad Implícita

```python
implied_vol_biseccion(precio_mercado, S, K, T, r, q, tipo, tol=1e-6)
```
Calcula IV usando el método de Brent.

---

## Parámetros de Mercado Utilizados

| Parámetro | Valor | Descripción |
|-----------|-------|-------------|
| `r` | 4.5% | Tasa libre de riesgo (Fed Funds) |
| `q` | 1.3% | Dividend yield de SPY |
| `IV` | ~18% | Volatilidad implícita promedio asumida |

---

## Resultados del Backtest

El backtest de **Long Straddle mensual** (2024-2025) muestra:

| Métrica | Valor |
|---------|-------|
| Total de trades | 23 |
| P&L promedio | -$255.58 |
| % Rentables | 43.5% |
| IV asumida | 18% |
| Vol realizada promedio | 13.8% |

**Conclusión:** La estrategia sistemática de compra de volatilidad resulta perdedora cuando la volatilidad implícita supera consistentemente a la realizada (IV > RV), fenómeno conocido como *variance risk premium*.

---

## Notas Importantes

1. **Tipo de cuenta:** El notebook detecta automáticamente si estás en paper trading o cuenta real, mostrando advertencias apropiadas.

2. **Datos delayed:** Se utiliza `marketDataType=4` (frozen/delayed) para cuentas sin suscripción completa.

3. **Errores de strikes:** Algunos strikes pueden no existir para ciertos vencimientos; el código maneja estos casos gracefully.

4. **SPY vs SPX:**
   - SPY: Opciones americanas, liquidación física, afectadas por dividendos
   - SPX: Opciones europeas, liquidación en efectivo, sin ejercicio anticipado

---

## Licencia

Material educativo desarrollado para el programa MIAX 14.

---

## Referencias

- Hull, J. (2018). *Options, Futures, and Other Derivatives*
- Natenberg, S. (1994). *Option Volatility and Pricing*
- [Interactive Brokers API Documentation](https://interactivebrokers.github.io/tws-api/)
- [ib_insync Documentation](https://ib-insync.readthedocs.io/)
