# Tarea Mandatory

## Visualización de Datos con Visual Studio Code

En esta tarea realizaremos una comparación sobre el Mapa de España con todas sus Comunidades Autónomas mostrando con un círculo la ocupación de las camas UCI's tanto al prinicpio de la pandemia como en la situación actual.

Para mostar el mapa de España, lo saco de la siguiente url:
https://github.com/deldersveld/topojson/blob/master/countries/spain/spain-comunidad-with-canary-islands.json

A la hora de marcar el círculo con la ocupación de las camas UCI´s sobre cada Comunidad Autónoma utilizaremos la longitud y latitud para trazarlo, implemento el archivo que he utilizado.

```sh
export const latLongCommunities = [
  {
    name: "Comunidad de Madrid",
    long: -3.70256,
    lat: 40.4165,
  },
  {
    name: "Andalucía",
    long: -4.5,
    lat: 37.6,
  },
  {
    name: "Comunidad Valenciana",
    long: -0.37739,
    lat: 39.45975,
  },
  {
    name: "Región de Murcia",
    long: -1.13004,
    lat: 37.98704,
  },
  {
    name: "Extremadura",
    long: -6.16667,
    lat: 39.16667,
  },
  {
    name: "Cataluña",
    long: 1.86768,
    lat: 41.82046,
  },
  {
    name: "País Vasco",
    long: -2.75,
    lat: 43.0,
  },
  {
    name: "Cantabria",
    long: -4.03333,
    lat: 43.2,
  },
  {
    name: "Principado de Asturias",
    long: -5.86112,
    lat: 43.36662,
  },
  {
    name: "Galicia",
    long: -7.86621,
    lat: 42.75508,
  },
  {
    name: "Aragón",
    long: -1.0,
    lat: 41.0,
  },
  {
    name: "Castilla y León",
    long: -4.45,
    lat: 41.383333,
  },
  {
    name: "Castilla-La Mancha",
    long: -3.000033,
    lat: 39.500011,
  },
  {
    name: "Islas Canarias",
    long: -15.5,
    lat: 28.0,
  },
  {
    name: "Islas Baleares",
    long: 2.52136,
    lat: 39.18969,
  },
  {
    name: "Comunidad Foral de Navarra",
    long: -1.65,
    lat: 42.816666,
  },
  {
    name: "La Rioja",
    long: -2.445556,
    lat: 42.465,
  },
  {
    name: "Ceuta",
    long: -5.344104,
    lat: 35.898369,
  },
  {
    name: "Melilla",
    long: -2.9464,
    lat: 35.301432,
  },
];
```

Como vamos a realizar una comparación entre el principio y la actualidad de la pandemia, pondremos 2 botones para ver el cambio de ocupación en los hospitales:

```sh
<html>
  <head>
    <link rel="stylesheet" type="text/css" href="./map.css" />
    <link rel="stylesheet" type="text/css" href="./base.css" />
    <link rel="stylesheet" type="text/css" href="./styles.css" />
  </head>
  <body>
    <div>
      <button id="Inicio">Initial Data</button>
      <button id="Final">Final Data</button>
    </div>
      <script src="./index.ts"></script>
  </body>
</html>
```

Importaremos las estadísticas de los Datos Iniciales y los Datos Iniciales con el siguiente comando, ambos dentro de data.ts. Los datos finales han sido obtenidos del Ministerio de Sanidad, según la ocupación en camas UCI´s.

```sh
import {
  stats,
  stats2,
  ResultEntry
} from "./data"
```

```sh
export interface ResultEntry {
  name: string;
  value: number;
}

export const stats : ResultEntry[]= [
  {
    name: "Madrid",
    value: 174,
  },
  {
    name: "La Rioja",
    value: 39,
  },
  {
    name: "Andalucía",
    value: 34,
  },
  {
    name: "Cataluña",
    value: 24,
  },
  {
    name: "Valencia",
    value: 30,
  },
  {
    name: "Murcia",
    value: 0,
  },
  {
    name: "Extremadura",
    value: 6,
  },
  {
    name: "Castilla La Mancha",
    value: 16,
  },
  {
    name: "País Vasco",
    value: 45,
  },
  {
    name: "Cantabria",
    value: 10,
  },
  {
    name: "Asturias",
    value: 5,
  },
  {
    name: "Galicia",
    value: 3,
  },
  {
    name: "Aragón",
    value: 11,
  },
  {
    name: "Castilla y León",
    value: 19,
  },
  {
    name: "Islas Canarias",
    value: 18,
  },
  {
    name: "Islas Baleares",
    value: 6,
  },
];

export const stats2 : ResultEntry[]= [
  {
    name: "Madrid",
    value: 460,
  },
  {
    name: "La Rioja",
    value: 19,
  },
  {
    name: "Andalucía",
    value: 282,
  },
  {
    name: "Cataluña",
    value: 472,
  },
  {
    name: "Valencia",
    value: 79,
  },
  {
    name: "Murcia",
    value: 33,
  },
  {
    name: "Extremadura",
    value: 15,
  },
  {
    name: "Castilla La Mancha",
    value: 58,
  },
  {
    name: "País Vasco",
    value: 79,
  },
  {
    name: "Cantabria",
    value: 19,
  },
  {
    name: "Asturias",
    value: 66,
  },
  {
    name: "Galicia",
    value: 30,
  },
  {
    name: "Aragón",
    value: 51,
  },
  {
    name: "Castilla y León",
    value: 131,
  },
  {
    name: "Islas Canarias",
    value: 80,
  },
  {
    name: "Islas Baleares",
    value: 19,
  },
];

```

Según cada Comunidad Autónoma, el numero de hospitalizados es distinto, por lo que haremos una relación directa entre el radio de nuestro círculo y el numero de hospitalizados en camas UCI's, para ello definimos la funcion calculateRadiusBasedOnAffectedCases:

```sh
const calculateRadiusBasedOnAffectedCases = (comunidad: string, s: ResultEntry[]) => {
  const entry = s.find((item) => item.name === comunidad);

  return entry ? affectedRadiusScale(entry.value) : 0;
};
```

Para relizar la comparación, agregaremos un método para intercambiar los datos que estamos usando, tanto los iniciales como los finales:


```sh
const updateRadius = (data: ResultEntry[]) => {
  d3
  .selectAll("circle")
  .data(latLongCommunities)
  .attr("class", "affected-marker")
  .attr("r", d => calculateRadiusBasedOnAffectedCases(d.name, data))
  .attr("cx", (d) => aProjection([d.long, d.lat])[0])
  .attr("cy", (d) => aProjection([d.long, d.lat])[1]);
};
```

Hacemos una relación directa de los datos iniciales y actuales con cada botón:

```sh
document
    .getElementById("Inicio")
    .addEventListener("click", function handlResultsIni() {
      updateRadius(stats);
    });
  
document
    .getElementById("Final")
    .addEventListener("click", function handlResultsFinal() {
      updateRadius(stats2);
    });
```
