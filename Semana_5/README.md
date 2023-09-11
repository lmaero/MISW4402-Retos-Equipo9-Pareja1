# Reto capa de datos - Semana 5

## Integrantes - Equipo 9 - Pareja 1

|Nombre|Correo|
|------|------|
|Diego Fernando Eslava Lozano|d.eslava@uniandes.edu.co|
|Luis Miguel Guzman Perez|lm.guzmanp1@uniandes.edu.co|

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

2. Reporte de la comparación de los resultados de las pruebas de carga actual para Postgre y Timescale. Soportar la comparación con capturas de pantalla de los resultados de Jmeter
3. Explicación del porqué de los resultados apoyándose en los conceptos estudiados acerca de la capa de datos de un sistema IoT.
