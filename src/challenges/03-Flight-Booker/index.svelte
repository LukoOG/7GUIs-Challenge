<script lang='ts'>
    type Options = "one-way" | "return"

    let selected = $state<Options>('one-way')
        
    const getDate = () =>{
        let today = new Date()
        let [month, date, year] = today
        .toLocaleDateString('en-us', {year: "numeric", month: "2-digit", day: "2-digit"})
        .split('/')
        return `${year}-${month}-${date}`
    }

    let maxDate = getDate()
        
    let startDate = $state(getDate())
    let returnDate = $state(getDate())

    const handleSubmit= (e: Event) => {
        e.preventDefault();
        alert(`You have booked a ${selected} flight on ${startDate}`)
    }

    $inspect({startDate, returnDate, selected})
</script>

<form onsubmit={handleSubmit} class='grid-space'>
    <select bind:value={selected}>
        <option value='one-way'>One way Flight</option>
        <option value='return'>Return Flight</option>
    </select>

    <label>
        <span>Pick Start Date</span>
        <input type='date' min={getDate()} bind:value={startDate}>
    </label>

    <label>
        <span>Pick End Date:</span>
        <input type='date' disabled={selected != "return"} min={startDate} bind:value={returnDate}>
    </label>

    <button disabled={selected == "return" && returnDate < startDate}>
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