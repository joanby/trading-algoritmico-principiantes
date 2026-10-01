# Cambios de la rama `update-2026`

> Esta rama es el mismo curso —Trading Algorítmico para Principiantes—, con el código adaptado a las librerías de hoy
> (matplotlib 3.11.2, pandas 3.0.6, yfinance 1.7.0; octubre de 2026). La rama principal sigue exactamente como en el vídeo.

> **Qué está comprobado y qué no.** Se ha ejecutado cada notebook entero con las versiones de
> `requirements.txt`: **8 correctos · 0 con fallo · 0 con timeout · 3 omitidos**. Comprobado: que cada celda se ejecuta sin error y que los datos
> tienen la forma del vídeo (histórico completo, mismas columnas, mismo orden). **No comprobado:** la
> descarga real desde Yahoo Finance, que no era accesible desde el entorno de prueba; se usó una réplica
> de su respuesta con precios inventados. **Tus números saldrán distintos** a los del vídeo, porque los
> precios reales han seguido moviéndose.
> Sin ejecutar: `Capitulo_09_MT5`, `ES_TA_Capítulo_09_MT5_Trading_en_Vivo_Random`, `ES_TA_Capítulo_09_MT5_Trading_en_Vivo_SMA` (MetaTrader 5 solo funciona en Windows, con el terminal y una cuenta).

## Cómo usarla

- **En Google Colab (como en el vídeo):** abre el notebook de esta rama y, cuando el código lea un CSV,
  súbelo al panel de archivos igual que en el vídeo.
- **En tu ordenador:** `git clone -b update-2026 https://github.com/joanby/trading-algoritmico-principiantes`, instala `pip install -r requirements.txt`
  (versiones **fijadas**: las mismas con las que se ha comprobado, para que un cambio futuro de las
  librerías no lo vuelva a romper) y deja junto al notebook los CSV que use.

## Qué ha cambiado y por qué

### 1. Descargar precios con yfinance

Notebooks: `ES_TA_Capítulo_03_Estadística_Descriptiva`, `ES_TA_Capítulo_04_Pre_Procesado_de_Datos`, `ES_TA_Capítulo_05_Crea_tu_primera_estrategia_de_trading`, `ES_TA_Capítulo_06_Backtesting_Vectorizado`, `ES_TA_Capítulo_07_Estrategias_de_Trading_Intermedias`, `ES_TA_Capítulo_08_Conceptos_Avanzados_de_Trading`.

yfinance cambió tres comportamientos por defecto de `yf.download` desde que se grabó el curso
(comprobado leyendo yfinance 0.1.70, la versión de entonces, y la 1.7.0 de hoy):

| En el vídeo | Hoy, si no dices nada | Qué pasa con el código del curso |
|---|---|---|
| Sin fechas, descarga **todo el histórico** | Descarga **solo el último mes** | Medias largas vacías, `.loc["2020"]` da `KeyError`, backtests de un mes |
| Columnas `Open, High, Low, Close, Adj Close, Volume` | Sin `Adj Close` (`auto_adjust=True`) | `KeyError: 'Adj Close'` y *Length mismatch* al renombrar |
| Columnas simples, en ese orden | Dos niveles (precio, ticker) y en **orden alfabético** | Aunque arregles lo anterior, al renombrar por posición `open` acabaría siendo `Adj Close` |

Cada `yf.download(...)` lleva ahora los argumentos que devuelven el comportamiento del vídeo:

```python
yf.download("EURUSD=X", period="max", auto_adjust=False, multi_level_index=False)
```

`period="max"` solo se añade cuando la llamada no tenía fechas. Donde el código renombra las columnas por
posición (`df.columns = ["open", "high", ...]`), antes se reordenan como estaban:
`[["Open", "High", "Low", "Close", "Adj Close", "Volume"]]`.

**Si escribes el código siguiendo el vídeo**, añade esos argumentos en tu `yf.download`.

`yf.Ticker(...).history()` no ha cambiado (ya ajustaba precios y bajaba un mes por defecto). Y los dobles
corchetes de `df[["Close"]].rolling(15).mean()` **no son un fallo**: funcionan igual en pandas 3.

### 2. Rutas de Colab

Notebooks: `ES_TA_Capítulo_02_Python_para_Data_Science`, `ES_TA_Capítulo_04_Pre_Procesado_de_Datos`.

`pd.read_csv("/content/fichero.csv")` → `pd.read_csv("fichero.csv")`. En Colab es lo mismo (la carpeta de trabajo es `/content`) y además funciona en tu ordenador.

### 3. El módulo de MetaTrader 5 se importaba con otro nombre

Notebooks: `ES_TA_Capítulo_09_MT5_Trading_en_Vivo_Random`, `ES_TA_Capítulo_09_MT5_Trading_en_Vivo_SMA`.

Los notebooks importaban `Chapter_09_MT5`, pero el fichero del repositorio se llama `Capitulo_09_MT5.py`: ahora importan ese. **Sin ejecutar** (solo Windows).

### 4. Errores que el vídeo provoca a propósito

Notebooks: `ES_TA_Capítulo_01_Los_fundamentos_de_Python`, `ES_TA_Capítulo_09_MT5_Trading_en_Vivo_Random`, `ES_TA_Capítulo_09_MT5_Trading_en_Vivo_SMA`.

Algunas celdas dan un error a propósito para explicar algo (por ejemplo, qué es una variable local). Siguen dándolo; solo se han marcado (`raises-exception`) para que *Ejecutar todo* no se pare ahí.
