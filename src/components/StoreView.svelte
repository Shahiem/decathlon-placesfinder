<script>
    import { createEventDispatcher } from "svelte";

    export let properties, selectedSports;

    const dispatch = createEventDispatcher();

    function buildQuery(array) {
        let queryString = '';

        let key = 0;
        Object.keys(array).forEach((paramKey) => {
            let paramValue = array[paramKey];
            if (paramValue) {
                let paramSymbol = (key >= 1 ? ',' : '');
                queryString += paramSymbol + encodeURI(paramValue);

                key++;
            }
        });

        return queryString;
    }
    
    function handleClick() {
        let address = [
            properties.address_components.address,
            properties.address_components.postal_code,
            properties.address_components.city,
            properties.address_components.country,
            properties.address_components.postal_code,
        ]

        dispatch("route", {
            q: buildQuery(address)
        });
    }
</script>

<div class="store">
    <div class="store__info">
        <h3 class="store__name">{properties.name}</h3>
        <div class="store__address">
            {#if properties.address_components.address}
                <span>{properties.address_components.address}</span>
            {/if}

            <span>
                {#if properties.address_components.postal_code}
                {properties.address_components.postal_code},
                {/if}

                {#if properties.address_components.city}
                {properties.address_components.city},
                {/if}

                {#if properties.address_components.country}
                {properties.address_components.country}
                {/if}
            </span>

            {#if properties.contact_details.phone}
                <span>{properties.contact_details.phone}</span>
            {/if}
        </div>

        <div class="store__actions">
            <a href="#" on:click={handleClick}>Route</a>
        </div>

        <div class="store__icons">
            {#each properties.activities as { sport_id } }
                {#if selectedSports.indexOf(sport_id.toString()) >= 0}
                    <div class="store__icon">
                        <img src="https://sports-api-production.s3.amazonaws.com/uploads/sport/icon/{sport_id}/{sport_id}.svg" alt="Sport">
                    </div>
                {/if}
            {/each}
        </div>
    </div>
</div>

<style type="text/scss">
    @import '../styles/main';
</style>