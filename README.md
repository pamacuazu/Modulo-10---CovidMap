# Modulo 10 - Visualizaciones

## Objetivo Tarea:

Esta tarea consiste en hacer visualizar un mapa de España con todas las comunidades autonomas mostrando con un circulo la gravedad de los contagios desde la primera ola hasta situación actual.
Para ello compararemos el total de casos acumulados de cada comunidad tomando como referencia la comunidad con mayor número de casos.


## Datos: 

Los datos fueron seleccionados de:
https://www.eldiario.es/sociedad/mapa-datos-coronavirus-espana-comunidades-autonomas-febrero-11_1_1039633.html


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














