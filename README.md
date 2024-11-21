# Practica6-IoT2
# Proyecto: API de Datos Climatológicos con MicroPython

## 1. API de Datos Climatológicos
Usaremos la API de OpenWeatherMap para obtener datos climáticos. La URL base para los datos de una ciudad es:


- Reemplaza `{CITY}` por la ciudad deseada.
- Reemplaza `{YOUR_API_KEY}` por tu clave de API.

---

## 2. Leer los Datos con MicroPython
A continuación, el código para el ESP32 que conecta al Wi-Fi, obtiene datos de la API y selecciona 5 datos para mostrar.

### Código en MicroPython
```python
import network
import urequests
import time

# Configuración Wi-Fi
SSID = "Filia_Bobarin"
PASSWORD = "AndresLArgo2020"

def conectar_wifi():
    wlan = network.WLAN(network.STA_IF)
    wlan.active(True)
    wlan.connect(SSID, PASSWORD)

    while not wlan.isconnected():
        print("Conectando al WiFi...")
        time.sleep(1)

    print("Conectado:", wlan.ifconfig())

# Leer datos de la API
def obtener_datos_clima(ciudad, api_key):
    url = f"http://api.openweathermap.org/data/2.5/weather?q={ciudad}&appid={api_key}&units=metric"
    try:
        response = urequests.get(url)
        datos = response.json()
        response.close()

        # Extraer 5 datos preferidos
        print("Clima en:", datos["name"])
        print("Temperatura:", datos["main"]["temp"], "°C")
        print("Humedad:", datos["main"]["humidity"], "%")
        print("Velocidad del viento:", datos["wind"]["speed"], "m/s")
        print("Presión:", datos["main"]["pressure"], "hPa")
        print("Descripción:", datos["weather"][0]["description"])

        # Retornar datos para graficar
        return {
            "temp": datos["main"]["temp"],
            "humidity": datos["main"]["humidity"],
            "wind": datos["wind"]["speed"],
            "pressure": datos["main"]["pressure"],
            "description": datos["weather"][0]["description"]
        }
    except Exception as e:
        print("Error al obtener datos:", e)
        return None

# Main
conectar_wifi()
api_key = "TU_API_KEY"
ciudad = "Potosi"
datos_clima = obtener_datos_clima(ciudad, api_key)
```python
## 4. Graficar los Datos
Usaremos **Highcharts** para crear un gráfico en un archivo HTML. Este archivo mostrará los datos enviados.

### Código HTML con Highcharts
Guarda este código en un archivo `.html` (por ejemplo, `graficos.html`).

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Datos Climatológicos</title>
    <script src="https://code.highcharts.com/highcharts.js"></script>
</head>
<body>
    <h1>Gráficos de Datos Climatológicos</h1>
    <div id="grafico" style="width: 100%; height: 400px;"></div>

    <script>
        // Datos simulados (sustituir por los reales enviados por MicroPython)
        const datosClima = {
            temperatura: 20, // Ejemplo
            humedad: 60,
            viento: 3.5,
            presion: 1012,
            descripcion: "cielo claro"
        };

        // Crear gráfico
        Highcharts.chart('grafico', {
            chart: {
                type: 'column'
            },
            title: {
                text: 'Datos Climatológicos'
            },
            xAxis: {
                categories: ['Temperatura', 'Humedad', 'Viento', 'Presión']
            },
            yAxis: {
                title: {
                    text: 'Valores'
                }
            },
            series: [{
                name: 'Valores',
                data: [
                    datosClima.temperatura,
                    datosClima.humedad,
                    datosClima.viento,
                    datosClima.presion
                ]
            }]
        });
    </script>
</body>
</html>
