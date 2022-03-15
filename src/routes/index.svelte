<script context="module">
    export async function load ({ fetch }) {
        const response = await fetch(`https://pokeapi.co/api/v2/pokemon/?limit=10&offset=0`);
        const data = await response.json();

        if (response.ok) {
            return {
                status: response.status,
                props: {
                    pokemons: data.results,
                    nextPageUrl: data.next,
                    totalCount: data.count,
                    displayedCount: 10,
                }
            }
        }

        return {
            status: response.status,
            error: new Error('Failed to fetch pokemons')
        }
    }
</script>

<script>
    import Card from '$lib/components/card.svelte';

    export let pokemons;
    export let nextPageUrl;
    export let totalCount;
    export let displayedCount;
    let isLoading = false;

    async function loadMorePokemons() {
        isLoading = true;

        fetch(nextPageUrl)
            .then(response => {
                if (response.ok) return response.json();
                else throw new Error('Failed to fetch pokemons');
            }).then(data => {
                pokemons = [ ...pokemons, ...data.results ];
                displayedCount += 10;
                nextPageUrl = data.next;
                isLoading = false;
            }).catch(error => {
                isLoading = false;
            });
    }
</script>

<main>
    <header class="text-center flex justify-center items-center flex-col pt-28 pb-6">
        <img src="/pokeball.svg" alt="pokeball" class="w-28 h-28 my-4 hover:rotate-45 transition-transform" />
        <h1 class="text-5xl font-bold text-teal-800">Welcome to PokemonDB</h1>
        <!-- <p>Currently showing {displayedCount} out of {totalCount}</p> -->
    </header>
    <section class="flex gap-3 mx-40">
        <Card cardTitle="Pokemons" cardData={pokemons}/>
        <Card cardData={[]}/>
        <Card cardData={[]}/>
    </section>
    
    <!-- {#if isLoading}
        <p>Fetching pokemons...</p>
    {:else }
        <button on:click={loadMorePokemons}>Load more...</button>
    {/if} -->
</main>