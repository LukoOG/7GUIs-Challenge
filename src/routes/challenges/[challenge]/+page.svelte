<script lang="ts">
    import { page } from "$app/state";
    const challenge = $derived(import(`../../../challenges/${page.params.challenge}/index.svelte`))
</script>

{#await challenge then component}
    <component.default/>
{/await}

<style>

</style>