<script>
    import { getDataByURL } from './helpers/FetchApi.svelte';
    import Search from './components/Search.svelte';
    import StoreView from './components/StoreView.svelte';

    let storeResults = [];
    let selectedSports = [];
    let searchedAddress = 'Decathlon';

    // Create a query string
    function buildQuery(array) {
        let queryString = '?';

        Object.keys(array).forEach((paramKey, key) => {
            let paramValue = array[paramKey];

            if (paramValue !== '') {
                let query = paramKey + '=' + paramValue;
                let paramSymbol = (key >= 1 ? '&' : '');

                queryString += paramSymbol + query;
            }
        });

        return queryString;
    }

    function findRoute(event) {
        // Change location in google maps
       searchedAddress = event.detail.q;
    }

    // Get data from places API
    async function fetchPlaces(params) {
        let results = getDataByURL('https://sportplaces.api.decathlon.com/api/v1/places' + buildQuery(params));
        storeResults = results;
    }
    
    // Handle search submit
    async function searchPlaces(event) {
        fetchPlaces(event.detail);
        selectedSports =  event.detail.sports;
    }
</script>

<div class="container">
    <div class="app">
        <div class="app__left">
            <div class="app__logo">
                <img src="images/decathlon-logo.svg" alt="Decathlon">
            </div>

            <h1>Places finder</h1>

            <Search on:submit={searchPlaces} />

            {#await storeResults}
                <div class="preloader"><img src="images/preloader.gif" alt="Decathlon"></div>

                {:then store}
                    {#if store.features}
                        {#if store.features.length >= 1}
                            <div class="stores">
                                {#each store.features as {properties} }
                                <StoreView {selectedSports} on:route={findRoute} bind:properties={properties}></StoreView>
                                {/each}
                            </div>
                        {:else}
                           No results found!
                        {/if}
                    {/if}
                {:catch error}
                    An error occured              
            {/await}
        </div>

        <div class="app__right">
            <iframe title="Map" width="100%" height="100%" frameborder="0" style="border:0;" src="https://www.google.com/maps/embed/v1/place?q={searchedAddress}&key=AIzaSyCjwiEMudPJoQ-1WMfjSTeMZ0H1IPTolJw" allowfullscreen></iframe>
        </div>
    </div>
</div>

<style type="text/scss">
  @import './styles/main';
</style>