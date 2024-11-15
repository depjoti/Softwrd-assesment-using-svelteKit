<script>
  import { onMount } from 'svelte';
  import 'ol/ol.css';
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
  let vectorSource;
  let vectorLayer;

  onMount(() => {
    initializeMap();
    return () => {
      if (map) {
        map.setTarget(undefined);
      }
    };
  });

  function initializeMap() {
    // Create vector source and layer
    vectorSource = new VectorSource();
    vectorLayer = new VectorLayer({
      source: vectorSource
    });

    // Initialize map
    map = new Map({
      target: mapElement,
      layers: [
        new TileLayer({
          source: new OSM()
        }),
        vectorLayer
      ],
      view: new View({
        center: fromLonLat([-95, 38]),
        zoom: 3
      })
    });

    // Initial update of features
    updateMapFeatures();
  }

  function updateMapFeatures() {
    if (!vectorSource) return;

    // Clear existing features
    vectorSource.clear();

    // Add new features
    landingPads.forEach(pad => {
      if (pad.location.longitude && pad.location.latitude) {
        const feature = new Feature({
          geometry: new Point(fromLonLat([pad.location.longitude, pad.location.latitude])),
          properties: pad
        });

        // Set style based on status
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
  }

  // Watch for changes in landingPads and update features
  $: if (map && vectorSource && landingPads) {
    updateMapFeatures();
  }
</script>

<div bind:this={mapElement} class="h-[400px] w-full rounded-lg overflow-hidden"></div>

<style>
  :global(.ol-control) {
    background-color: transparent;
  }

  :global(.ol-zoom) {
    padding: 0;
    margin: 1rem;
  }

  :global(.ol-zoom button) {
    background-color: white !important;
    color: #4b5563 !important;
    border: 1px solid #e5e7eb !important;
    margin: 1px !important;
    border-radius: 0.25rem !important;
  }
</style>