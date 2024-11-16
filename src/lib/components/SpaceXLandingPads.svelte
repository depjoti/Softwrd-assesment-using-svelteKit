<script>
  import { Table, TableBody, TableBodyCell, TableBodyRow, TableHead, TableHeadCell } from 'flowbite-svelte';
  import { Button, ButtonGroup, Card, Modal } from 'flowbite-svelte';
  import { ListOutline, GridOutline, LinkOutline, FilterOutline, ChevronDownOutline } from 'flowbite-svelte-icons';
  import { selectedStatus } from '../stores/filterStore';

  export let landingPads = [];
  let viewMode = 'list';
  let showModal = false;
  let selectedPad = null;
  let showFilterDropdown = false;

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

  function formatStatus(status) {
    return status.toUpperCase();
  }

  function openDetails(pad) {
    selectedPad = pad;
    showModal = true;
  }

  // function handleStatusSelect(status) {
  //   selectedStatus.set(status);
  //   showFilterDropdown = false;
  // }

  function handleStatusSelect(status) {
    showFilterDropdown = false;
    setTimeout(() => {
      selectedStatus.set(status);
    }, 100);
  }
</script>

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
    <div class="relative inline-block">
      <Button 
        color="light" 
        class="flex items-center gap-2"
        on:click={() => showFilterDropdown = !showFilterDropdown}
      >
        <FilterOutline class="w-4 h-4" />
        Filter By Status
        <ChevronDownOutline class="w-3 h-3 ml-2" />
      </Button>

      {#if showFilterDropdown}
        <div class="absolute right-0 mt-2 w-64 bg-white rounded-lg shadow-lg border border-gray-200 z-50">
          <div class="p-3 space-y-1">
            <label class="flex items-center gap-3 cursor-pointer hover:bg-gray-50 p-2 rounded-md">
              <input 
                type="radio" 
                class="w-4 h-4 text-blue-600"
                name="status-filter"
                checked={$selectedStatus === 'all'}
                on:change={() => handleStatusSelect('all')}
              />
              <span class="text-gray-900">All</span>
            </label>
            <label class="flex items-center gap-3 cursor-pointer hover:bg-gray-50 p-2 rounded-md">
              <input 
                type="radio" 
                class="w-4 h-4 text-blue-600"
                name="status-filter"
                checked={$selectedStatus === 'active'}
                on:change={() => handleStatusSelect('active')}
              />
              <div class="flex items-center gap-2">
                <div class="w-2 h-2 rounded-full bg-green-500"></div>
                <span class="text-gray-900">Active</span>
              </div>
            </label>
            <label class="flex items-center gap-3 cursor-pointer hover:bg-gray-50 p-2 rounded-md">
              <input 
                type="radio" 
                class="w-4 h-4 text-blue-600"
                name="status-filter"
                checked={$selectedStatus === 'retired'}
                on:change={() => handleStatusSelect('retired')}
              />
              <div class="flex items-center gap-2">
                <div class="w-2 h-2 rounded-full bg-red-500"></div>
                <span class="text-gray-900">Retired</span>
              </div>
            </label>
            <label class="flex items-center gap-3 cursor-pointer hover:bg-gray-50 p-2 rounded-md">
              <input 
                type="radio" 
                class="w-4 h-4 text-blue-600"
                name="status-filter"
                checked={$selectedStatus === 'under construction'}
                on:change={() => handleStatusSelect('under construction')}
              />
              <div class="flex items-center gap-2">
                <div class="w-2 h-2 rounded-full bg-blue-500"></div>
                <span class="text-gray-900">Under Construction</span>
              </div>
            </label>
          </div>
        </div>
      {/if}
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
                {formatStatus(pad.status)}
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
              {formatStatus(pad.status)}
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

<style>
  input[type="radio"] {
    accent-color: #3b82f6;
  }
</style>

<svelte:window on:click={(e) => {
  const filterButton = e.target.closest('button');
  const filterDropdown = e.target.closest('.relative');
  
  if (!filterButton && !filterDropdown) {
    showFilterDropdown = false;
  }
}} />