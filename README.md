# Modulo 10 - Visualizaciones

## Objetivo Tarea:

Esta tarea consiste en hacer visualizar un mapa de España con todas las comunidades autonomas mostrando con un circulo la gravedad de los contagios desde la primera ola hasta situación actual.
Para ello compararemos el total de casos acumulados de cada comunidad tomando como referencia la comunidad con mayor número de casos.


## Datos: 

Los datos fueron seleccionados de:
https://www.eldiario.es/sociedad/mapa-datos-coronavirus-espana-comunidades-autonomas-febrero-11_1_1039633.html
(estos servirán para el fichero -> ```src/stats.ts```

## Guía:

1. Creamos una carpeta de proyecto con los siguientes archivos:
    - scr/ -> directorio
    - package.json -> archivo de configuración de npm
    - tsconfig.json -> fichero con la configuración de typecript

2. Instalar en terminal:

```
npm install
```

Cuando trabajamos con mapas, tenemos que instalar otros paquetes necesarios:
```
npm install topojson-client --save
```
```
npm install @types/topojson-client --save-dev
```
Para el proyecto necesitaremos ademas:
```
npm install d3-composite-projections --save
```
```
npm install @types/node --save-dev
```

3. Vamos a darle forma al directorio, creando los archivos necesarios:

- src/index.ts: se encuentra la receta necesaria para crear el mapa
- src/communities.ts: contiene la información con la latitud y longitud de las comunidades autónomas de España
- src/stats.ts: contiene información sobre los casos de COVID-19 por CCAA
- src/index.html: contiene el código HTML del proyecto
- src/map.css: contiene el código css del proyecto.

4. Para el fichero ```scr/map.css``` vamos a crear dos clases:

country: Class for the country color and community lines.
affected-marker: Class for the orange circles.

    - "country": esta contiene la clase para el color del país y las lineas de las comunidades.
    - "affected-marker: clase para los circulos que indican los casos en cada CCAA

5. Para el topojson de España, hemos extraído la información del siguiente enlace:

https://github.com/deldersveld/topojson/blob/master/countries/spain/spain-comunidad-with-canary-islands.json

Esto irá en el fichero -> ```./src/spain.json```

## Código Mapa de España

Lo realizamos en el fichero -> ```/src/index.ts```

- Importar todas las dependencias que necesitaremos:

```typescript
const aProjection = d3Composite
  .geoConicConformalSpain() // Let's make the map bigger to fit in our resolution
  .scale(3300)
  // Let's center the map
  .translate([500, 400]);

const geoPath = d3.geoPath().projection(aProjection);
const geojson = topojson.feature(spainjson, spainjson.objects.ESP_adm1);
);
```

- Estructura del mapa de España:

```typescript
const aProjection = d3Composite
  .geoConicConformalSpain() // Let's make the map bigger to fit in our resolution
  .scale(3300)
  // Let's center the map
  .translate([500, 400]);

const geoPath = d3.geoPath().projection(aProjection);
const geojson = topojson.feature(spainjson, spainjson.objects.ESP_adm1);
);
```

- Creamos el mapa:

```typescript
const svg = d3
  .select("body")
  .append("svg")
  .attr("width", 1024)
  .attr("height", 800)
  .attr("style", "background-color: #FBFAF0");

svg
  .selectAll("path")
  .data(geojson["features"])
  .enter()
  .append("path")
  .attr("class", "country")
  // data loaded from json file
  .attr("d", geoPath as any);
```

- Cogemos como referencia el número de casos de la CCAA más afectada:

```typescript
const calculateMaxAffected = (dataset: ResultEntry[]) => {
  return dataset.reduce(
    (max, item) => (item.value > max ? item.value : max),
    0
  );
};
```

- Para escalar el radio del círculo, vamos a coger el número máximo de la incidencia acumulada de todos los datos.

```typescript
const calculateAffectedRadiusScale = (maxAffected: number) => {
  return d3.scaleLinear().domain([0, maxAffected]).range([0, 20]);
};
```

```typescript
const calculateRadiusBasedOnAffectedCases = (
  comunidad: string,
  dataset: ResultEntry[]
) => {
  const maxAffected = calculateMaxAffected(dataset);

  const affectedRadiusScale = calculateAffectedRadiusScale(maxAffected);

  const entry = dataset.find((item) => item.name === comunidad);

  const adder = d3
    .scaleThreshold<number, number>()
    .domain([0, 1000, 10000, 100000])
    .range([1, 5, 10, 20]);

  return entry ? affectedRadiusScale(entry.value) + adder(maxAffected) : 0;
};
```

- Actualización del gráfico al hacer clic


```typescript
const updateChart = (dataset: ResultEntry[]) => {
  svg
    .selectAll("circle")
    .data(latLongCommunities)
    .attr("class", "affected-marker")
    .attr("cx", (d) => aProjection([d.long, d.lat])[0])
    .attr("cy", (d) => aProjection([d.long, d.lat])[1])
    .transition()
    .duration(800)
    .attr("r", (d) => calculateRadiusBasedOnAffectedCases(d.name, dataset));
};
```
- Lógica del botón


```typescript
document
  .getElementById("initial")
  .addEventListener("click", function handleInitialStats() {
    updateChart(initialStats);
  });

document
  .getElementById("final")
  .addEventListener("click", function handleFinalStats() {
    updateChart(finalStats);
  });
```

- Añadimos las estadísticas iniciales al mapa

```typescript
svg
    .selectAll("circle")
    .data(latLongCommunities)
    .enter()
    .append("circle")
    .attr("class", "affected-marker")
    .attr("cx", (d) => aProjection([d.long, d.lat])[0])
    .attr("cy", (d) => aProjection([d.long, d.lat])[1])
    .attr("r", (d) => calculateRadiusBasedOnAffectedCases(d.name, initialStats));
```
## Resultado final:

![task1](https://user-images.githubusercontent.com/58860865/153750633-478421bb-f2c0-4213-ac2b-308bb869eb76.PNG)




















