<script>
    import { createEventDispatcher } from "svelte";
    import { getDataByURL } from "../helpers/FetchApi.svelte";
    import Carousel from "./Carousel.svelte";

    let selectedSports = [];
    let selectedCountry;

    function handleClick() {
        let sportId = this.dataset.id;

        // Check if sport not exists
        if (!selectedSports.includes(sportId)) {
            selectedSports.push(sportId);

            this.style.background = "#fff";
            this.style.border = "1px solid #d9f0fc";
        } else {
            //  Remove sport if it exists
            let item = selectedSports.indexOf(sportId);

            if (item !== -1) {
                selectedSports.splice(item, 1);

                this.style.background = "#f6fcff";
                this.style.border = "none";
            }
        }

        selectedSports = selectedSports;
    }

    const dispatch = createEventDispatcher();

    function handleSubmit() {
        dispatch("submit", {
            sports: selectedSports,
            country: selectedCountry
        });
    }

    // Get data from sport API
    async function fetchSports() {
        let results = getDataByURL("https://sports.api.decathlon.com/sports?has_icon=true");
        return results;
    }

    let sports = fetchSports();
</script>

{#await sports}
    <div class="preloader">
        <img src="images/preloader.gif" alt="Decathlon">
    </div>

    {:then sport}
    <div class="form">  
        <h3 class="form__title">Which sports would you like to do?</h3>
        <div id="sports" class="sports">
            <Carousel controls={false} perPage={{ 1800: 6, 800:3 , 500: 4 }}>
                {#each sport as { attributes } (attributes)}
                    <div class="slide-content">
                        <button
                            class={selectedSports[attributes.decathlon_id] ? 'sport sport--selected' : 'sport'}
                            data-id={attributes.decathlon_id}
                            on:click={handleClick}>
                            <img src={attributes.icon} alt={attributes.name} />
                            <span class="sport__name">{attributes.name}</span>
                        </button>
                    </div>
                {/each}
            </Carousel>
        </div>

        <h3 class="form__title">Where?</h3>
        <div class="form__group">
            <input type="text" class="form__input" placeholder="Place or ZIP code" disabled>
        </div>

        <div class="form__group">
            <select class="form__select" bind:value={selectedCountry}>
                <option value="NL">Netherlands</option>
                <option value="CA">Canada</option>
            </select>
        </div>


        <h3 class="form__title">Radius</h3>
        <div class="form__group">
            <select class="form__select" disabled>
                <option value="10">5 km</option>
                <option value="10">10 km</option>
                <option value="15">15 km</option>
                <option value="20">20 km</option>
                <option value="10">25 km</option>
            </select>
        </div>

        <div class="form__group">
            <button class="form__btn" on:click={handleSubmit}>Search</button>
        </div>
    </div>
{:catch error}
    An error occured
{/await}

<style type="text/scss">
  @import "../styles/main";
</style>
