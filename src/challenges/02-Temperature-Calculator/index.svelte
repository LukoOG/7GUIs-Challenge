<script lang='ts'>
    class Temperature{
        #celcius = $state(0)
        #farhenheit = $state(0)
        
        constructor(celcius: number, farhenheit: number){
            this.#celcius = celcius
            this.#farhenheit = farhenheit
        }

        get celcius(){
            return this.#celcius
        }
        
        set celcius(value){
            this.#celcius = value
            this.#farhenheit = (value * 9/5) + 32
        }
        
        get farhenheit(){
            return this.#farhenheit
            
        }
        
        set farhenheit(value){
            this.#farhenheit = value
            this.#celcius = (value - 32) * 5/9
        }
    }

    let temperature: Temperature = new Temperature(0, 0)
</script>

<div class='flex-gap'>
    <label>
        <input type="number" bind:value={temperature.celcius}>
    </label>
    Celcius
    =
    <label>
        <input type="number" bind:value={temperature.farhenheit}>
    </label>
    Farhenheit
</div>

<style>
    
</style>