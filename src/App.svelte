<script>
  import { onMount } from 'svelte';
  import 'ol/ol.css';  
  import SpaceXLandingPads from './lib/components/SpaceXLandingPads.svelte';
  import LandingPadsMap from './lib/components/LandingPadsMap.svelte';

  let landingPads = [];

  onMount(async () => {
    try {
      const response = await fetch('https://api.spacexdata.com/v3/landpads');
      landingPads = await response.json();
    } catch (error) {
      console.error('Error fetching landing pads:', error);
    }
  });
</script>

<main class="container mx-auto p-4">
  <div class="grid grid-cols-1 lg:grid-cols-[1fr,400px] gap-6">
    <div class="bg-white rounded-lg shadow-sm">
      <SpaceXLandingPads {landingPads} />
    </div>
    <div class="bg-white rounded-lg shadow-sm">
      <h2 class="p-4 text-lg font-medium border-b">Map View</h2>
      <div class="p-4">
        <LandingPadsMap {landingPads} />
      </div>
    </div>
  </div>
</main>

<style>
  :global(body) {
    background-color: #f9fafb;
  }
</style>