<script lang='ts'>
    let counter = $state(0)
</script>

<div class='flex-gap'>
        <input type='number' bind:value={counter} readonly>
        <button onclick={()=>counter++}>
            increment
        </button>
</div>

<style>
    button{
        text-decoration: none;
        background-color: #111;
        outline: none;
        border: none;
    }
</style>