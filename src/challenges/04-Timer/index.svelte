<script lang='ts'>
    let d = $state(3) //duration of the timer
    let e = $state(0) //elapsed time since timer mounted

    let interval: number

    function start(){
        interval = setInterval(() => {
            e+=0.1
            if(e > d){
                console.log(interval)
                clearInterval(interval)
            }
        }, 100);
    }
    
    function reset() {
        clearInterval(interval)
        e = 0
    }

    $effect(() => {
        if(!d || d < e  || e > d) return //this makes it reactive to d, so that when d is increased, the timer starts again. d < e makes sure  it doesn't react if it's decreased
        start()
        return ()=>{clearInterval(interval)}
    })
</script>

<div class='grid-gap'>
    <div>
        <label>
            <span>Elapsed Time:</span>
            <progress value={e} max={d}>
        </label> 
        
		<div>{e.toFixed(1)}s</div>
    </div>

    <input type="range" bind:value={d}  max=10>
    <button onclick={reset}>Reset</button>
</div>

<style>
    button{
        text-decoration: none;
        background-color: #111;
        outline: none;
        border: none;
    }
</style>