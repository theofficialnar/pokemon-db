<script context="module">
    export async function load ({ fetch }) {
        const fetchPokemons = await fetch('https://pokeapi.co/api/v2/pokemon/?limit=10&offset=0');
        const fetchMoves = await fetch('https://pokeapi.co/api/v2/move/?limit=10&offset=0');
        const fetchLocations = await fetch('https://pokeapi.co/api/v2/location/?limit=10&offset=0');

        return Promise.all([fetchPokemons, fetchMoves, fetchLocations])
            .then(async ([ pokemonsResponse, movesResponse, locationsResponse ]) => {
                if (pokemonsResponse.ok && movesResponse.ok && locationsResponse.ok) {
                    return [
                        await pokemonsResponse.json(),
                        await movesResponse.json(),
                        await locationsResponse.json(),
                    ];
                } else {
                    throw new Error('Failed to fetch data');
                }
            }).then(([ pokemons, moves, locations ]) => {
                console.log(pokemons, moves, locations)
                return {
                    props: {
                        pokemons: pokemons.results,
                        moves: moves.results,
                        locations: locations.results,
                    }
                };
            }).catch(error => {
                return {
                    status: error.status,
                    error: error.message,
                };
            });
    }
</script>

<script>
    import Card from '$lib/components/card.svelte';

    export let pokemons;
    export let moves;
    export let locations;
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
        <Card cardTitle="🐗 Pokemons" cardData={pokemons}/>
        <Card cardTitle="⚡ Moves" cardData={moves}/>
        <Card cardTitle="🗺️ Locations" cardData={locations}/>
    </section>
    
    <!-- {#if isLoading}
        <p>Fetching pokemons...</p>
    {:else }
        <button on:click={loadMorePokemons}>Load more...</button>
    {/if} -->
</main>