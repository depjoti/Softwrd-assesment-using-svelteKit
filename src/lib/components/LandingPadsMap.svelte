<script>
  import { onMount } from 'svelte';
  import Map from 'ol/Map';
  import View from 'ol/View';
  import TileLayer from 'ol/layer/Tile';
  import VectorLayer from 'ol/layer/Vector';
  import VectorSource from 'ol/source/Vector';
  import OSM from 'ol/source/OSM';
  import Feature from 'ol/Feature';
  import Point from 'ol/geom/Point';
  import { fromLonLat } from 'ol/proj';
  import { Circle as CircleStyle, Fill, Stroke, Style } from 'ol/style';

  export let landingPads = [];
  let mapElement;
  let map;

  $: if (mapElement && landingPads.length > 0) {
    initMap();
  }

  function initMap() {
    // Create vector source and features
    const vectorSource = new VectorSource();
    
    landingPads.forEach(pad => {
      if (pad.location.longitude && pad.location.latitude) {
        const feature = new Feature({
          geometry: new Point(fromLonLat([pad.location.longitude, pad.location.latitude])),
          properties: pad
        });

        // Style based on status
        const color = pad.status === 'active' ? '#22c55e' : 
                     pad.status === 'retired' ? '#ef4444' : '#3b82f6';

        feature.setStyle(new Style({
          image: new CircleStyle({
            radius: 8,
            fill: new Fill({ color }),
            stroke: new Stroke({
              color: '#ffffff',
              width: 2
            })
          })
        }));

        vectorSource.addFeature(feature);
      }
    });

    // Create map if it doesn't exist
    if (!map) {
      map = new Map({
        target: mapElement,
        layers: [
          new TileLayer({
            source: new OSM()
          }),
          new VectorLayer({
            source: vectorSource
          })
        ],
        view: new View({
          center: fromLonLat([-95, 38]),
          zoom: 4
        })
      });
    }
  }

  onMount(() => {
    if (landingPads.length > 0) {
      initMap();
    }

    return () => {
      if (map) {
        map.setTarget(undefined);
      }
    };
  });
</script>

<div bind:this={mapElement} class="map-container"></div>

<style>
  .map-container {
    width: 100%;
    height: 400px;
    border-radius: 0.5rem;
    overflow: hidden;
  }

  :global(.ol-control) {
    background-color: transparent;
  }

  :global(.ol-zoom) {
    padding: 0;
    margin: 1rem;
  }

  :global(.ol-zoom button) {
    background-color: white;
    color: #4b5563;
    border: 1px solid #e5e7eb;
    margin: 1px;
    border-radius: 0.25rem;
  }
</style>