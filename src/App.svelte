<script>
  import { onMount } from 'svelte';
  import { selectedStatus } from './lib/stores/filterStore';
  import SpaceXLandingPads from './lib/components/SpaceXLandingPads.svelte';
  import LandingPadsMap from './lib/components/LandingPadsMap.svelte';
  import LandingPadsChart from './lib/components/LandingPadsChart.svelte';

  let landingPads = [];
  let filteredPads = [];

  $: filteredPads = landingPads.filter(pad => {
    if ($selectedStatus === 'all') return true;
    return pad.status.toLowerCase() === $selectedStatus.toLowerCase();
  });

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
    <SpaceXLandingPads landingPads={filteredPads} />
    <div class="flex flex-col gap-6">
      <div class="bg-white rounded-lg shadow-sm">
        <h2 class="p-4 text-lg font-medium border-b">Map View</h2>
        <div class="p-4">
          <LandingPadsMap landingPads={filteredPads} />
        </div>
      </div>
      <LandingPadsChart landingPads={filteredPads} />
    </div>
  </div>
</main>

<style>
  :global(body) {
    background-color: #f9fafb;
  }
</style>