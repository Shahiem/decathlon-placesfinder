<script context="module">
    // Get JSON data from API
    export async function getDataByURL(url) {
        let response = await fetch(url);
        let data = await response.json();

        return data.data;
    }
</script>
