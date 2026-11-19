# Cambios de la rama `update-2026`

> Esta rama es el mismo curso —Trading Algorítmico para principiantes: de cero a héroe—, con el código adaptado a las librerías de hoy
> (matplotlib 3.11.2, pandas 3.0.6, yfinance 1.7.0; octubre de 2026). La rama principal sigue exactamente como en el vídeo.

> **Qué está comprobado y qué no.** Se ha ejecutado cada notebook entero con las versiones de
> `requirements.txt`: **8 correctos · 0 con fallo · 0 con timeout · 3 omitidos**. Comprobado: que cada celda se ejecuta sin error y que los datos
> tienen la forma del vídeo (histórico completo, mismas columnas, mismo orden). **No comprobado:** la
> descarga real desde Yahoo Finance, que no era accesible desde el entorno de prueba; se usó una réplica
> de su respuesta con precios inventados. **Tus números saldrán distintos** a los del vídeo, porque los
> precios reales han seguido moviéndose.
> Sin ejecutar: `Capitulo_09_MT5`, `ES_TA_Capítulo_09_MT5_Trading_en_Vivo_Random`, `ES_TA_Capítulo_09_MT5_Trading_en_Vivo_SMA` (MetaTrader 5 solo funciona en Windows, con el terminal y una cuenta).
> En noviembre de 2026 se corrigieron además errores del propio código del curso, que dan resultados
> distintos a los del vídeo: ver [Correcciones de errores del curso](#correcciones-de-errores-del-curso-noviembre-de-2026).

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
| Con solo `end="2021-01-01"`, desde el principio hasta esa fecha | **Solo el mes anterior** a esa fecha | El mismo problema, sin ningún error |
| Columnas `Open, High, Low, Close, Adj Close, Volume` | Sin `Adj Close` (`auto_adjust=True`) | `KeyError: 'Adj Close'` y *Length mismatch* al renombrar |
| Columnas simples, en ese orden | Dos niveles (precio, ticker) y en **orden alfabético** | Aunque arregles lo anterior, al renombrar por posición `open` acabaría siendo `Adj Close` |

Cada `yf.download(...)` lleva ahora los argumentos que devuelven el comportamiento del vídeo:

```python
yf.download("EURUSD=X", period="max", auto_adjust=False, multi_level_index=False)
```

`period="max"` se añade siempre que la llamada no tenga fecha de inicio (`start`). Donde el código renombra las columnas por
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

## Correcciones de errores del curso (noviembre de 2026)

Aquí no ha cambiado ninguna librería: son fallos del código del curso que enseñaban algo mal. **Lo que
verás en pantalla será distinto del vídeo**, y está explicado en cada punto. Comprobado: los capítulos 6 y 7 se han
vuelto a ejecutar enteros con `requirements.txt` y la réplica de Yahoo de arriba (todos sin error). Las
cifras de antes y después salen de Yahoo Finance real, descargado el 19 de noviembre de 2026. Los notebooks de MetaTrader 5 **no se han ejecutado** (solo Windows).
Los notebooks conservan la salida guardada del vídeo: en las celdas de estos puntos, lo guardado es el
resultado antiguo (por ejemplo, `Alpha: 0.9 %`); al ejecutarlas, sale el nuevo.

### 1. Alfa (capítulos 6 y 7)

**En el vídeo:** alfa se calcula como `252 * mean * (1-beta)` (celda 13) y, dentro de `BackTest`, como
`serie.mean() - beta*serie.mean()`. Sale un 0,9 % para Google. El `BackTest` del capítulo 7 (celda 37) hace el mismo cálculo.

**Qué estaba mal:** las dos expresiones son el retorno medio de la cartera multiplicado por (1 − beta).
Ahí no aparece el mercado, así que no dicen si la cartera lo ha hecho mejor que él, y el signo puede salir
al revés que el real. La fórmula escrita en la celda 12 tenía el mismo fallo.

**Ahora:** es el alfa de Jensen, con tipo libre de riesgo cero: el retorno medio de la cartera menos beta
por el retorno medio del SP500, anualizado igual que antes.

```python
alpha = (252 * (mean - beta*val["SP500"].mean()))*100                        # celda 13
alpha = annualiazed_scalar * (serie.mean() - beta*val["SP500"].mean())       # BackTest
```

La fórmula de la celda 12 pasa a ser $r_{PF} - r_f - \beta (r_M - r_f)$. `BackTest` se llama igual y
recibe lo mismo.

**Qué cambia en los números** (alfa anual):

| | Antes | Ahora |
|---|---|---|
| Google 2004–2020 (beta 0,965), celda 13 | 0,9 % | **17,2 %** |
| `BackTest(return_serie)`, celda 21 | 0,918 % | 17,231 % |
| SMA sobre `EURUSD=X` (Yahoo), celda 25 | 1,418 % | 1,479 % |
| SMA sobre `EURUSD_D1.csv`, celda 26 | 2,58 % | 2,447 % |
| Cartera del capítulo 7, celda 38 | 12,9 % | 12,914 % |

Alfa no mide seguridad: es la rentabilidad que se obtiene por encima de la que tocaría por el riesgo de
mercado asumido (beta). Con beta cercana a 0 apenas cambia; con beta cercana a 1, el cálculo antiguo daba
casi 0 fuera cual fuera el alfa real.

### 2. `preprocessing_yf` descargaba siempre EURUSD (capítulos 6 y 7)

**Qué estaba mal:** recibe `symbol`, pero descargaba siempre `"EURUSD=X"`. Con cualquier otro símbolo
devolvía EURUSD sin avisar.

**Ahora:** `yf.download(symbol, ...)`, como la `preprocessing_yf` de los capítulos 4 y 5.

**Qué cambia en los números:** nada en el curso, que la llama con `"EURUSD=X"` (el bucle de la celda 34 del
capítulo 7 usa la `preprocessing_yf` de la celda 29, que ya estaba bien). Cambia si la usas con otro activo.

### 3. Capítulo 9 (MetaTrader 5): instalación y avisos (sin ejecutar)

- **Celda 0 de los dos notebooks:** se han comentado `numpy==1.19.2` y `scipy==1.6.2`, que no se
  instalan en un Python actual: numpy 1.19.2 solo trae instaladores hasta Python 3.8, y scipy 1.6.2 no
  admite Python 3.10 o posterior (en 3.12, pip intenta compilar numpy y falla). También se ha comentado
  `pip install datetime`: `datetime` viene con Python, y en PyPI ese nombre es otro paquete. `numpy` se
  sigue instalando, porque la piden `ta` y `MetaTrader5`. Si instalas en un entorno virtual, quita también
  `--user`, que ahí da error.
- **Notebook SMA:** la orden se llamaba `"Bitcoin"`, pero opera `EURUSD` con 1 lote. Ahora se llama
  `"Euro vs US dollar"`, como en el notebook Random. El símbolo y el volumen no cambian.
- **Avisos, sin cambiar el código:** hay un comentario en `filling_mode - 1` (en `Capitulo_09_MT5.py`) y otro en
  los `while True` de los dos notebooks. `filling_mode` es una máscara de bits del broker: restarle 1 solo acierta si el
  símbolo admite un único modo de ejecución, y si no, la orden puede rechazarse. El bucle no tiene ninguna
  pausa: repite la ronda durante todo el segundo de arranque y luego da vueltas sin parar. Opera
  siempre en una **cuenta demo**, y empieza con `live = False`.
