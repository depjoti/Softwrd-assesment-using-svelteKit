
<script>
  import { onMount } from 'svelte';
  import SpaceXLandingPads from './lib/components/SpaceXLandingPads.svelte';

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

<main>
  <SpaceXLandingPads {landingPads} />
</main>

<style>
  main {
    width: 100%;
    min-height: 100vh;
  }
</style>