<script lang='ts'>
    type Options = "one-way" | "return"

    let selected = $state<Options>('one-way')
</script>

<form class='grid-space'>
    <select bind:value={selected}>
        <option value='one-way'>One way Flight</option>
        <option value='return'>Return Flight</option>
    </select>

    <label>
        <span>Pick Start Date</span>
        <input>
    </label>

    <label>
        <span>Pick End Date:</span>
        <input>
    </label>

    <button>
        Book
    </button>
</form>

<style>
    form{
        width: 50%;
        margin: 0 auto;
        padding: 20px;
    }
</style>