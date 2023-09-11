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

| Instancia         | Endpoint   | # Samples | Average | Min | Max   | Std. Dev. | Error % | Throughput | Received KB/sec | Sent KB/sec | Avg. Bytes |
| ----------------- | ---------- | --------- | ------- | --- | ----- | --------- | ------- | ---------- | --------------- | ----------- | ---------- |
| Postgres (local)  | Primario   | 60        | 43989   | 0   | 46330 | 3299.45   | 0.00%   | 1.3/sec    | 0.79            | 0.17        | 627.0      |
| Postgres (local)  | Secundario | 60        | 23703   | 0   | 25015 | 1673.30   | 0.00%   | 2.4/sec    | 1.46            | 0.40        | 627.9      |
| Postgres (EC2)    | Primario   | 60        | 44895   | 0   | 47938 | 5132.58   | 0.00%   | 1.2/sec    | 0.76            | 0.16        | 628.0      |
| Postgres (EC2)    | Secundario | 60        | 24182   | 0   | 25761 | 2484.56   | 0.00%   | 2.3/sec    | 1.40            | 0.37        | 629.2      |
| Timescale (local) | Primario   | 60        | 18784   | 0   | 21920 | 2025.63   | 0.00%   | 2.7/sec    | 1.63            | 0.35        | 624.0      |
| Timescale (local) | Secundario | 60        | 6083    | 0   | 7249  | 489.88    | 0.00%   | 7.3/sec    | 4.42            | 1.21        | 619.0      |
| Timescale (EC2)   | Primario   | 60        | 20951   | 0   | 21450 | 294.18    | 0.00%   | 2.8/sec    | 5.65            | 0.34        | 625.0      |
| Timescale (EC2)   | Secundario | 60        | 6784    | 0   | 7093  | 71.14     | 0.00%   | 7.5/sec    | 7.41            | 1.17        | 623.0      |

A continuación se adjuntan algunas de las capturas de pantalla para soportar los datos de la tabla anterior

Variables (local)
<img width="1728" alt="Screenshot 2023-09-11 at 11 47 10 AM" src="https://github.com/lmaero/MISW4402-Retos-Equipo9-Pareja1/assets/60992168/b2b39cf6-c718-417a-8d5c-ba09a7ed75b0">

Postgres (local) Primario

<img width="1728" alt="Screenshot 2023-09-11 at 11 46 46 AM" src="https://github.com/lmaero/MISW4402-Retos-Equipo9-Pareja1/assets/60992168/1bd1e990-bf6b-4618-b9c9-64b1c127e797">

Variables (EC2)
<img width="1728" alt="Screenshot 2023-09-11 at 1 04 53 PM" src="https://github.com/lmaero/MISW4402-Retos-Equipo9-Pareja1/assets/60992168/88dcabc1-2bd2-4cd7-bb8e-b80d26086ce2">

Postgres (EC2) Primario
<img width="1728" alt="Screenshot 2023-09-11 at 1 04 38 PM" src="https://github.com/lmaero/MISW4402-Retos-Equipo9-Pareja1/assets/60992168/7be8e495-5c1c-43c6-88eb-63e71eacfe5e">


3. Explicación del porqué de los resultados apoyándose en los conceptos estudiados acerca de la capa de datos de un sistema IoT.
