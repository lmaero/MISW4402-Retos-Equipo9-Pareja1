# Reto capa de datos - Semana 5

## Integrantes - Equipo 9 - Pareja 1

| Nombre                       | Correo                      |
| ---------------------------- | --------------------------- |
| Diego Fernando Eslava Lozano | d.eslava@uniandes.edu.co    |
| Luis Miguel Guzman Perez     | lm.guzmanp1@uniandes.edu.co |

## Desarrollo del reto

### Modificación Aplicaciones

#### Enlace al repositorio
[GitHub - Semana 5](https://github.com/lmaero/MISW4402-Retos-Equipo9-Pareja1/tree/main/Semana_5)

#### Razonamiento
Se decidió establecer la posibilidad de consultar el promedio del histórico total de mediciones, el valor mínimo y el valor máximo, de todas las variables para las cuales existe un monitoreo continuo, en este caso, humedad y temperatura. Además se permite la consulta específica de los datos pertenecientes a un rango de fechas específico, usando query params como marcas de tiempo (timestamps).

Se considera una consulta de alto consumo de recursos debido a que se retorna y calcula el promedio, valor mínimo y valor máximo, para todos los datos existentes en la base de datos, en este caso 500.000, lo cual hace interesante la comparación de rendimiento entre las dos consultas, permitiendo marcar una clara diferencia.

#### Cambios en el código
A modo general se hicieron algunas optimizaciones de formato e importación de dependencias en los archivos modificados, `views.py` y `urls.py`, de las dos aplicaciones.

* En el enlace a continuación del commit [83e4c3b](https://github.com/lmaero/MISW4402-Retos-Equipo9-Pareja1/commit/83e4c3b679b18ae394235aa782c68d0d61ea3528) se relacionan los cambios de la aplicación de monitoreo de Postgres.

* En el enlace a continuación del commit [b2a3e9a](https://github.com/lmaero/MISW4402-Retos-Equipo9-Pareja1/commit/b2a3e9a78c32607deb0618fb409581a3ebaff863) se relacionan los cambios de la aplicación de monitoreo de Timescale.

#### Endpoint
Los endpoint asociados a la solicitud son:

* Principal: /data-analysis/
* Secundario (filtro): /data-analysis/?start=1624233600000&end=1624825600000
  * En este ejemplo se filtran los datos con fecha de inicio `Monday, June 21, 2021 12:00:00 AM` y fecha de finalización: `Sunday, June 27, 2021 8:26:40 PM`

#### Estructura JSON
A continuación se detalla la estructura elegida para la respuesta de los endpoints, aplicable para el principal y el secundario.

```json
{
  "$schema": "http://json-schema.org/draft-04/schema#",
  "type": "object",
  "properties": {
    "measurement_name": {
      "type": "string"
    },
    "measurement_unit": {
      "type": "string"
    },
    "average_value": {
      "type": "number"
    },
    "max_value_in_range": {
      "type": "number"
    },
    "min_value_in_range": {
      "type": "number"
    }
  },
  "required": [
    "measurement_name",
    "measurement_unit",
    "average_value",
    "max_value_in_range",
    "min_value_in_range"
  ]
}
```

### Despliegue y pruebas de carga

#### Despliegue
Para el despliegue de la aplicación se decidió realizar pruebas en el `localhost` conectado a la base de datos alimentada previamente en el tutorial. Esto con el fin de minimizar los riesgos o inconsistencias detectados en las librerías de las dos aplicaciones, ya que estas no están contenerizadas y dependen de la configuración de la máquina en donde se ejecuta.

Adicionalmente se consideró de interés realizar la comparativa de resultados ejecutando la aplicación en una instancia distinta a la EC2 de la base de datos, es decir, en la máquina local.

#### Resultados de las pruebas de carga

Nota: los valores iguales se suprimen de los resultados para dar claridad a la visualización y comparación:
* N Samples = 60
* Min = 0
* Error % = 0.00%

| Item | Instancia         | Endpoint   | Average | Max   | Std. Dev.   | Throughput  | Received KB/sec | Sent KB/sec | Avg. Bytes |
| ---- | ----------------- | ---------- | ------- | ----- | ----------- | ----------- | --------------- | ----------- | ---------- |
| 1    | Postgres (local)  | Primario   | 43989   | 46330 | 3299.45     | 1.3/sec     | 0.79            | 0.17        | 627.0      |
| 2    | Postgres (EC2)    | Primario   | 44895   | 47938 | **5132.58** | 1.2/sec     | 0.76            | 0.16        | 628.0      |
| 3    | Postgres (local)  | Secundario | 23703   | 25015 | 1673.30     | **2.4/sec** | 1.46            | 0.40        | 627.9      |
| 4    | Postgres (EC2)    | Secundario | 24182   | 25761 | **2484.56** | 2.3/sec     | 1.40            | 0.37        | 629.2      |
| 5    | Timescale (local) | Primario   | 18784   | 21920 | 2025.63     | **2.7/sec** | 1.63            | 0.35        | 624.0      |
| 6    | Timescale (EC2)   | Primario   | 20951   | 21450 | **294.18**  | 2.8/sec     | 5.65            | 0.34        | 625.0      |
| 7    | Timescale (local) | Secundario | 6083    | 7249  | 489.88      | 7.3/sec     | 4.42            | 1.21        | 619.0      |
| 8    | Timescale (EC2)   | Secundario | 6784    | 7093  | **71.14**   | **7.5/sec** | 7.41            | 1.17        | 623.0      |

A continuación se adjuntan algunas de las capturas de pantalla para soportar los datos de la tabla anterior

Variables (local)
<img width="1728" alt="Screenshot 2023-09-11 at 11 47 10 AM" src="https://github.com/lmaero/MISW4402-Retos-Equipo9-Pareja1/assets/60992168/b2b39cf6-c718-417a-8d5c-ba09a7ed75b0">

Postgres (local) Primario

<img width="1728" alt="Screenshot 2023-09-11 at 11 46 46 AM" src="https://github.com/lmaero/MISW4402-Retos-Equipo9-Pareja1/assets/60992168/1bd1e990-bf6b-4618-b9c9-64b1c127e797">

Variables (EC2)
<img width="1728" alt="Screenshot 2023-09-11 at 1 04 53 PM" src="https://github.com/lmaero/MISW4402-Retos-Equipo9-Pareja1/assets/60992168/88dcabc1-2bd2-4cd7-bb8e-b80d26086ce2">

Postgres (EC2) Primario
<img width="1728" alt="Screenshot 2023-09-11 at 1 04 38 PM" src="https://github.com/lmaero/MISW4402-Retos-Equipo9-Pareja1/assets/60992168/7be8e495-5c1c-43c6-88eb-63e71eacfe5e">

### Explicación de los resultados
* Como primer punto es interesante realizar la comparación entre aplicación local con base de datos desplegada en EC2, y aplicación en EC2 con base de datos en EC2; la variación del promedio de respuesta es prácticamente insignificante, lo cual genera curiosidad respecto de la poca latencia introducida por la solicitud desde una máquina distinta a la de despliegue, probablemente se hubiera esperado una variación mayor.

* En segundo lugar, se puede apreciar como la desviación estándar para las instancias de Postgres aumenta considerablemente entre los dos ambientes,aproximadamente por un factor de 1.5; en contraste la desviación estandar de las instancias de Timescale disminuye en un factor cercano a 7.

* También es destacable para el tiempo de procesamiento (throughput) que ni siquiera el mayor rendimiento logrado (item 3 - en local) para la base de datos de Postgres, logra superar el menor (item 5 - local) logrado por Timescale; que se traduce en una marcada diferencia de los tiempos de procesamiento para las consultas.

* El mayor rendimiento lo logró la instancia EC2 de Timescale, 7.5/sec, lo cual marca una clara diferencia de más de 5 segundos con el rendimiento logrado por su contraparte de Postgres.

* El impacto de rendimiento fue claramente evidenciable una vez finalizado el tutorial y realizado el ejercicio comparativo, del cual se pueden extraer las siguienes conclusiones:

  * Los patrones elegidos para las dos bases de datos impactaron en la generación de datos, haciendo de Postgres la de mayor rendimiento, respecto de Timescale que tardó aproximadamente 2 horas más en finalizar la generación de los 500.000 registros.
  * Así mismo, los patrones impactan en los parámetros de rendimiento de las consultas, claramente evidenciables en los registros anteriores, y en los datos obtenidos del tutorial; en donde se marca una diferencia enorme en el promedio del tiempo de respuesta. Por ejemplo, para el endpoint primario existe un aumento en la eficiencia del tiempo de respuesta de aproximadamente el 114.28%, haciendo de Timescale y su patrón Blob, óptimo para este tipo de consultas. Y para el endpoint secundario un aumento aproximado del 256.45%.

