<script lang='ts'>
    let counter = $state(0)
</script>

<div class='challenge-container'>
        <input type='number' bind:value={counter} readonly>
        <button onclick={()=>counter++}>
            increment
        </button>
</div>

<style>
    .challenge-container{
        height: 90%;
        width: 90%;
        margin: 0 auto;
        display: flex;
    }
</style>