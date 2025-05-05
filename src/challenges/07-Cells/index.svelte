<script lang='ts'>
    const rows = 10
    const columns = 10
    const letters = "ABCDEFGHIJKLMNOPQRSTUVWXYZ"

    let data = $state([
        [{value:'Item'},{value:'Price'},{value:'Amount'},{value:'Total'}],
        [{value:'🍌'},{value:'20'},{value:'3'},{value:'=MULTIPLY(B2,C2)'}],
        [{value:'🍎'},{value:'40'},{value:'2'},{value:'=MULTIPLY(B3,C3)'}],
        // [{value:'🥭'},{value:'Price'},{value:'Total'},{value:'Amount'}],
        [{value:''},{value:''},{value:'Total'},{value:'=SUM(D2,D3)'}],
    ])

    let selectedCell = $state()
    let editedCell = $state()

    const parseValue = (val: string): string | number => {
        if(val.startsWith("=")){
            const { operation, cells } = parseFormula(val)
            const values = cells.map(({row, col}) => {
                let value = data[row][col].value
                if(value.startsWith("=")){
                    return +parseValue(value)
                }
                return +value
            })
        if(operation == "SUM") console.log(values)

            return values.reduce((a, b) => {
                if(operation == "MULTIPLY") return a * b;
                if(operation == "SUM") return a + b;
                return a
            }, operation == "MULTIPLY" ? 1 : 0)
        }
        return val
    }

    const parseFormula = (val: string) => {
        //e.g =MULTIPLY(B2,B3) -> {Multiply, [B2, B3]}
        const [a, b] = val.split("(")
        const operation = a.split("=")[1]
        const cells = b.split(")")[0].split(',')
        return {operation, cells: cells.map((cell)=>getCellIndex(cell))}
    }

    const getCellIndex = (cell: string) => {
        // A1 -> 00, C4 -> 23
        const [col, ...row] = cell.split('')
        return {row: +row.join("")-1, col: letters.indexOf(col)}
    }

    const updateCell = (e: Event) => {
        const {value, parentElement} = e.target as HTMLInputElement
        const { row, col } = getCellIndex(parentElement?.dataset.cell!)
        //updating base on different use cases

        //case 1, the row exists
        if(data[row]){
            //i. column is not empty: update value
            if(data[row][col]){
                data[row][col].value = value
            //ii. column is empty: create cell
            } else {
                data[row][col] = {value}
            }
        //case 2, row does not exist
        } else {
            //create row, then create cell
            data[row] = []
            data[row][col] = {value}
        }
    }

    console.log(parseFormula(data[2][3].value))
    console.log(parseFormula(data[3][3].value))
    console.log(getCellIndex("D2"))
</script>

<table>
    <thead>
        <tr>
            <th></th>
            {#each Array(rows) as _, i}
                <th>{letters[i]}</th>
            {/each}
        </tr>
    </thead>

    <tbody>
        {#each Array(rows) as _, i}
            <tr>
                <th>{i+1}</th>
                {#each Array(columns) as _, j}
                    {@const value = data[i]?.[j]?.value}
                    {@const cell =  `${letters[j]}${i+1}`}
                    {@const parsedValue = value ? parseValue(value) : ''}
                    {@const selected = selectedCell === cell}
                    {@const editing = editedCell === cell}
                    <td 
                    class:selected 
                    data-cell={cell}
                    onclick={() => {
                        if(selected) return
                        selectedCell = cell
                        editedCell = null
                    }}
                    ondblclick = {() => {
                        editedCell = cell
                    }}
                    >
                    {#if editing}
                    <!-- Cannot bind to constant, so define an update function elsewhere -->
                        <input type='text' oninput={updateCell} {value}>
                    {:else}
                        {parsedValue}
                    {/if}
                </td>
                {/each}
            </tr>
        {/each}
    </tbody>
</table>

<style>
    table{
        max-width: 900px;
        td{
            width: 160px;
            height: 50px;
            font-weight: 600;
            text-align: left;
            &.selected{
                outline: 1px solid var(--highlight);
                border-radius: var(--radius-1);
            }
            input{
                background: none;
                outline: none;
                width: 100%;
                padding: 0px;
            }
        }
    }
</style>