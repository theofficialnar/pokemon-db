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

<h1>Welcome to PokemonDB</h1>
<p>Currently showing {displayedCount} out of {totalCount}</p>
<ul>
{#each pokemons as pokemon}
    <li>{pokemon.name}</li>
{/each}
</ul>

{#if isLoading}
    <p>Fetching pokemons...</p>
{:else }
    <button on:click={loadMorePokemons}>Load more...</button>
{/if}