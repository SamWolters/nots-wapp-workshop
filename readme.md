# Maps 
NotS WAPP workshop


# Mapbox

<a href="https://docs.mapbox.com/mapbox-gl-js/guides/"><img src="https://assets.website-files.com/5d3ef00c73102c436bc83996/5d3ef00c73102c1f23c83a2a_logo-reversed.png" align="left" hspace="10" vspace="6"></a>

**Mapbox GL JS** is een client-side JavaScript-bibliotheek voor het bouwen van webkaarten en webapplicaties met behulp van de moderne cartografische technologie van Mapbox. Je kunt Mapbox GL JS gebruiken om Mapbox-kaarten weer te geven in een webbrowser of client, gebruikersinteractiviteit toe te voegen en de kaartervaring aan te passen in jouw applicatie.


## Resources

* [Installation](https://docs.mapbox.com/mapbox-gl-js/guides/install/)
* [API Reference](https://docs.mapbox.com/mapbox-gl-js/api/)
* [Examples](https://docs.mapbox.com/mapbox-gl-js/example/)
* [Plugins](https://docs.mapbox.com/mapbox-gl-js/plugins/)

# Tutorial
## Creeër een HTML file

Open je teksteditor en maak een nieuw bestand aan met de naam index.html. Stel dit nieuwe HTML-bestand in door de volgende code in je teksteditor te plakken. Deze code creëert de structuur van de pagina.

Er is een <div>-element met de ID "map" in het <body>-gedeelte van de pagina. Deze <div> is de container waarin de kaart op de pagina wordt weergegeven.

```html
<!DOCTYPE html>
<html lang='en'>
<head>
  <meta charset='utf-8' />
  <title>Local search app</title>
  <meta name='viewport' content='width=device-width, initial-scale=1' />
  <script src='https://api.mapbox.com/mapbox-gl-js/v2.14.1/mapbox-gl.js'></script>
  <link href='https://api.mapbox.com/mapbox-gl-js/v2.14.1/mapbox-gl.css' rel='stylesheet' />
  <style>
    body {
      margin: 0;
      padding: 0;
    }

    #map {
      position: absolute;
      top: 0;
      bottom: 0;
      width: 100%;
    }
  </style>
</head>
<body>

  <div id='map'></div>

</body>
</html>
```

## Initialiseer de map

Vervolgens voeg je de Mapbox GL JS-code toe die een kaart initialiseert, die wordt weergegeven in de <div> die je hebt aangemaakt in de vorige stap. In index.html plaats je het volgende fragment boven de afsluitende </body>-tag. Zorg ervoor dat je de constante mapbox.accessToken instelt op jouw Mapbox-toegangstoken.

```html
<script>
  mapboxgl.accessToken = 'YOUR_MAPBOX_TOKEN';
  const map = new mapboxgl.Map({
    container: 'map', 
    style: 'mapbox://styles/mapbox/streets-v12',
    center: [-122.25948, 37.87221], 
    zoom: 12, 
  });
</script>
```

Deze Mapbox GL JS-code stelt een stijl in voor de kaart, geeft de coördinaten op waarop het moet worden gecentreerd en stelt een zoomniveau in.

## Voeg een marker toe aan de map

De volgende stap is om een marker aan de kaart toe te voegen op de coördinaten van de campus om de locatie van het herkenningspunt weer te geven.

Voeg na de initialisatiecode die je in de vorige stap hebt geschreven het volgende fragment toe om een marker aan de kaart toe te voegen:

```javascript
const marker = new mapboxgl.Marker()
  .setLngLat([-122.25948, 37.87221]) 
  .addTo(map); 
```

Sla je wijzigingen op en vernieuw de pagina in je browser. Er zal een marker op de kaart verschijnen op de opgegeven coördinaten.

## Toevoegen van de geocoder

De volgende stap is om een geocoder toe te voegen met behulp van de Mapbox GL JS Geocoder-plugin. Om dit te doen, moet je eerst links naar de JavaScript- en CSS-bestanden van de geocoder toevoegen aan het head-gedeelte van het HTML-bestand.

```html
<script src='https://api.mapbox.com/mapbox-gl-js/plugins/mapbox-gl-geocoder/v4.7.0/mapbox-gl-geocoder.min.js'></script>
<link rel='stylesheet' href='https://api.mapbox.com/mapbox-gl-js/plugins/mapbox-gl-geocoder/v4.7.0/mapbox-gl-geocoder.css' type='text/css' />
```

Nadat deze links zijn toegevoegd, kun je de Mapbox GL JS Geocoder-plugin gebruiken in je app. Voeg vervolgens de volgende code toe boven de afsluitende </script>-tag in je HTML-bestand:

```js
const geocoder = new MapboxGeocoder({
  accessToken: mapboxgl.accessToken,
  placeholder: 'Search for places in Gelderland',
  mapboxgl: mapboxgl,
  marker: false 
});

map.addControl(geocoder);
```

Sla je wijzigingen op. Vernieuw de pagina in je browser en je zult zien dat er een zoekvak van de geocoder met aangepaste tekst aan de kaart is toegevoegd. Wanneer je een zoekterm in het vak typt en een resultaat selecteert, zal de kaart naar die locatie vliegen.

## Plaats markers op de geselecteerde resultaten

De laatste stap voor het maken van deze app is om een aangepaste marker op de kaart te plaatsen op de locatie van een geselecteerd zoekresultaat. De logica hiervoor moet worden ingepakt in een map.on('load')-gebeurtenis, zodat deze niet wordt geactiveerd voordat de kaart zelf is geladen. Plak vóór de afsluitende </script>-tag de volgende JavaScript:

```js
map.on('load', () => {
  map.addSource('single-point', {
    type: 'geojson',
    data: {
      type: 'FeatureCollection',
      features: []
    }
  });

  map.addLayer({
    id: 'point',
    source: 'single-point',
    type: 'circle',
    paint: {
      'circle-radius': 10,
      'circle-color': '#448ee4'
    }
  });

  geocoder.on('result', (event) => {
    map.getSource('single-point').setData(event.result.geometry);
  });
});
```
