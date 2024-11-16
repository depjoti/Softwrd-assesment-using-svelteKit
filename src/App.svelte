<script>
  import { onMount } from 'svelte';
  import { selectedStatus } from './lib/stores/filterStore';
  import SpaceXLandingPads from './lib/components/SpaceXLandingPads.svelte';
  import LandingPadsMap from './lib/components/LandingPadsMap.svelte';
  import LandingPadsChart from './lib/components/LandingPadsChart.svelte';
  import Loader from './lib/components/Loader.svelte';
  import spacexLogo from './assets/spacex-logo.png';

  let landingPads = [];
  let filteredPads = [];
  let isLoading = true;
  let isFiltering = false;

  $: {
    isFiltering = true;
    filteredPads = landingPads.filter(pad => {
      if ($selectedStatus === 'all') return true;
      return pad.status.toLowerCase() === $selectedStatus.toLowerCase();
    });
    // Small delay to show loading state during filtering
    setTimeout(() => {
      isFiltering = false;
    }, 300);
  }

  onMount(async () => {
    try {
      isLoading = true;
      const response = await fetch('https://api.spacexdata.com/v3/landpads');
      landingPads = await response.json();
    } catch (error) {
      console.error('Error fetching landing pads:', error);
    } finally {
      isLoading = false;
    }
  });
</script>

<main class="mx-auto">
  <!-- Header with SpaceX Logo -->
  <div class="bg-white border-b mb-4">
    <div class="container mx-auto px-4 py-6 flex justify-center">
      <img 
        src={spacexLogo} 
        alt="SpaceX" 
        class="h-8"
        style="max-width: none;"
      />
    </div>
  </div>

  <!-- Main Content -->
  <div class="container mx-auto px-4">
    {#if isLoading}
      <div class="flex justify-center items-center min-h-[400px]">
        <Loader size="w-12 h-12" />
      </div>
    {:else}
      <div class="grid grid-cols-1 lg:grid-cols-[1fr,400px] gap-6 relative">
        {#if isFiltering}
          <div class="absolute inset-0 bg-white/50 z-50 flex items-center justify-center">
            <Loader />
          </div>
        {/if}
        <SpaceXLandingPads landingPads={filteredPads} />
        <div class="flex flex-col gap-6">
          <div class="bg-white rounded-lg shadow-sm">
            <h2 class="p-4 text-lg font-medium border-b">Map View</h2>
            <div class="p-4">
              <LandingPadsMap landingPads={filteredPads} />
            </div>
          </div>
          <div>
            <LandingPadsChart landingPads={filteredPads} />
          </div>
          
        </div>
      </div>
    {/if}
  </div>
</main>

<style>
  :global(body) {
    background-color: #f9fafb;
  }
</style>