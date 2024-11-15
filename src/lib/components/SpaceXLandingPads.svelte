<script>
  import { Table, TableBody, TableBodyCell, TableBodyRow, TableHead, TableHeadCell } from 'flowbite-svelte';
  import { Button, ButtonGroup, Card, Modal } from 'flowbite-svelte';
  import { ListOutline, GridOutline, LinkOutline } from 'flowbite-svelte-icons';
  

  export let landingPads = [];
  let viewMode = 'list';
  let showModal = false;
  let selectedPad = null;

  function getSuccessRate(attempted_landings, successful_landings) {
    if (!attempted_landings) return 0;
    return ((successful_landings / attempted_landings) * 100).toFixed(1);
  }

  function getStatusColor(status) {
    switch(status.toLowerCase()) {
      case 'active':
        return 'text-green-500 bg-green-100 px-2 py-1 rounded-md text-sm';
      case 'retired':
        return 'text-red-500 bg-red-100 px-2 py-1 rounded-md text-sm';
      case 'under construction':
        return 'text-blue-500 bg-blue-100 px-2 py-1 rounded-md text-sm';
      default:
        return 'text-gray-500 bg-gray-100 px-2 py-1 rounded-md text-sm';
    }
  }

  function openDetails(pad) {
    selectedPad = pad;
    showModal = true;
  }
</script>

<!-- Modal Component -->
<Modal bind:open={showModal} size="md" title={`Details - ${selectedPad?.full_name}`} autoclose>
  {#if selectedPad}
   
    <p class="text-gray-600 text-sm leading-relaxed">
      {selectedPad.details}
    </p>
  {/if}
</Modal>

<div class="p-4">
  <div class="flex justify-between items-center mb-4">
    <div class="flex space-x-2">
      <ButtonGroup>
        <Button
          on:click={() => viewMode = 'list'}
          color={viewMode === 'list' ? 'primary' : 'light'}
          class="px-2 py-1"
        >
          <ListOutline size="sm" />
        </Button>
        <Button
          on:click={() => viewMode = 'grid'}
          color={viewMode === 'grid' ? 'primary' : 'light'}
          class="px-2 py-1"
        >
          <GridOutline size="sm" />
        </Button>
      </ButtonGroup>
    </div>
    <div>
      <Button color="light">
        Filter By Status
      </Button>
    </div>
  </div>

  {#if viewMode === 'list'}
    <Table noborder={true} hoverable={true} class="divide-y divide-gray-100">
      <TableHead>
        <TableHeadCell class="text-xs uppercase text-gray-500">Full Name</TableHeadCell>
        <TableHeadCell class="text-xs uppercase text-gray-500">Location Name</TableHeadCell>
        <TableHeadCell class="text-xs uppercase text-gray-500">Region</TableHeadCell>
        <TableHeadCell class="text-xs uppercase text-gray-500">Details</TableHeadCell>
        <TableHeadCell class="text-xs uppercase text-gray-500">Success Rate</TableHeadCell>
        <TableHeadCell class="text-xs uppercase text-gray-500">Wikipedia Link</TableHeadCell>
        <TableHeadCell class="text-xs uppercase text-gray-500">Status</TableHeadCell>
      </TableHead>
      <TableBody class="divide-y divide-gray-100">
        {#each landingPads as pad}
          <TableBodyRow>
            <TableBodyCell>{pad.full_name}</TableBodyCell>
            <TableBodyCell>{pad.location.name}</TableBodyCell>
            <TableBodyCell>{pad.location.region}</TableBodyCell>
            <TableBodyCell>
              <Button size="xs" color="light" class="px-3 py-1" on:click={() => openDetails(pad)}>
                View Details
              </Button>
            </TableBodyCell>
            <TableBodyCell>
              {#if pad.attempted_landings}
                <div class="flex items-center gap-2">
                  <div class="w-24 bg-gray-200 rounded-full h-2">
                    <div
                      class="bg-green-500 h-2 rounded-full"
                      style="width: {getSuccessRate(pad.attempted_landings, pad.successful_landings)}%"
                    ></div>
                  </div>
                  <span class="text-sm">{getSuccessRate(pad.attempted_landings, pad.successful_landings)}%</span>
                </div>
              {:else}
                <span class="text-sm">N/A</span>
              {/if}
            </TableBodyCell>
            <TableBodyCell>
              <a href={pad.wikipedia} target="_blank" rel="noopener noreferrer">
                <LinkOutline class="text-blue-500" size="sm" />
              </a>
            </TableBodyCell>
            <TableBodyCell>
              <span class={getStatusColor(pad.status)}>
                {pad.status}
              </span>
            </TableBodyCell>
          </TableBodyRow>
        {/each}
      </TableBody>
    </Table>
  {:else}
    <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
      {#each landingPads as pad}
        <Card padding="lg">
          <div class="flex justify-between items-start">
            <div>
              <h5 class="text-xl font-bold mb-2">{pad.full_name}</h5>
              <p class="text-gray-600 text-sm mb-1">Location: {pad.location.name}</p>
              <p class="text-gray-600 text-sm">Region: {pad.location.region}</p>
            </div>
            <span class={getStatusColor(pad.status)}>
              {pad.status}
            </span>
          </div>
          
          <div class="mt-4">
            <p class="text-sm text-gray-600 mb-2">Success Rate:</p>
            {#if pad.attempted_landings}
              <div class="flex items-center gap-2">
                <div class="w-full bg-gray-200 rounded-full h-2">
                  <div
                    class="bg-green-500 h-2 rounded-full"
                    style="width: {getSuccessRate(pad.attempted_landings, pad.successful_landings)}%"
                  ></div>
                </div>
                <span class="text-sm w-16">{getSuccessRate(pad.attempted_landings, pad.successful_landings)}%</span>
              </div>
            {:else}
              <span class="text-sm">N/A</span>
            {/if}
          </div>

          <div class="mt-4 flex justify-between items-center">
            <Button size="xs" color="light" class="px-3 py-1" on:click={() => openDetails(pad)}>
              View Details
            </Button>
            <a href={pad.wikipedia} target="_blank" rel="noopener noreferrer" class="text-blue-500 hover:text-blue-600">
              <LinkOutline size="sm" />
            </a>
          </div>
        </Card>
      {/each}
    </div>
  {/if}
</div>