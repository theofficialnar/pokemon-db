<script>
    export let cardData = [];
    export let cardTitle = '';
</script>

<div class="bg-indigo-200 rounded-md flex-auto divide-y divide-indigo-300 shadow-lg hover:shadow-2xl transition-shadow duration-500">
    <div class="p-5">
        <p class="font-bold text-xl text-indigo-900">{cardTitle}</p>
    </div>
    <div class="p-5">
        {#if !cardData.length}
            <p>No data...</p>
        {:else}
            <ul>
                {#each cardData as pokemon}
                    <li class="capitalize text-purple-900">{pokemon.name}</li>
                {/each}
            </ul>
            <p class="text-fuchsia-900 font-bold">See more...</p>
        {/if}
    </div>
</div>