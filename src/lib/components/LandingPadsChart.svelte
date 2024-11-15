<script>
  import { onMount } from 'svelte';
  import { Card } from 'flowbite-svelte';
  import { InfoCircleSolid } from 'flowbite-svelte-icons';
  import Chart from 'chart.js/auto';

  export let landingPads = [];
  let chartCanvas;
  let chart;

  $: if (chartCanvas && landingPads.length > 0) {
    initChart();
  }

  function initChart() {
    if (chart) {
      chart.destroy();
    }

    const ctx = chartCanvas.getContext('2d');
    
    const data = {
      labels: ['Active', 'Retired', 'Under Construction'],
      datasets: [{
        data: [
          landingPads.filter(pad => pad.status === 'active').length,
          landingPads.filter(pad => pad.status === 'retired').length,
          landingPads.filter(pad => pad.status === 'under construction').length
        ],
        backgroundColor: ['#22c55e', '#ef4444', '#3b82f6'],
        borderWidth: 0,
        cutout: '75%'
      }]
    };

    chart = new Chart(ctx, {
      type: 'doughnut',
      data: data,
      options: {
        responsive: true,
        maintainAspectRatio: false,
        plugins: {
          legend: {
            display: false
          }
        }
      }
    });
  }

  onMount(() => {
    if (landingPads.length > 0) {
      initChart();
    }

    return () => {
      if (chart) {
        chart.destroy();
      }
    };
  });
</script>

<Card>
  <div class="flex justify-between items-start w-full mb-4">
    <div>
      <div class="flex items-center gap-2">
        <h5 class="text-xl font-bold text-gray-900 dark:text-white">
          Success Rate Chart
        </h5>
        <InfoCircleSolid class="w-4 h-4 text-gray-500" />
      </div>
      <p class="text-sm text-gray-500 mt-1">
        Landing pad status distribution
      </p>
    </div>
  </div>

  <div class="relative h-64 w-full">
    <canvas bind:this={chartCanvas} />
    <div class="absolute top-1/2 left-1/2 transform -translate-x-1/2 -translate-y-1/2 text-center">
      <div class="text-3xl font-bold text-gray-900">{landingPads.length}</div>
      <div class="text-sm text-gray-500">Landing Pads</div>
    </div>
  </div>

  <div class="flex justify-center gap-6 mt-6">
    <div class="flex items-center gap-2">
      <div class="w-3 h-3 rounded-full bg-green-500"></div>
      <span class="text-sm text-gray-600">Active</span>
    </div>
    <div class="flex items-center gap-2">
      <div class="w-3 h-3 rounded-full bg-red-500"></div>
      <span class="text-sm text-gray-600">Retired</span>
    </div>
    <div class="flex items-center gap-2">
      <div class="w-3 h-3 rounded-full bg-blue-500"></div>
      <span class="text-sm text-gray-600">Under Construction</span>
    </div>
  </div>
</Card>