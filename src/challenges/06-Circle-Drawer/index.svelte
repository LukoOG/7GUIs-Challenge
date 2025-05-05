<script lang='ts'>
    type Circle = {
        id: string;
        cx:number;
        cy:number;
        r:number;
    }

    type Status = 'drawing' | 'editing'

    let circles = $state<Circle[]>([])
    let status = $state<Status>('drawing')
    let selected = $state<Circle>()!

    //the actions performed by the user
    let snapshots: Circle[][] = []
    //index to keep track of the history
    let history = $state(-1)


    const drawCircle = (e: MouseEvent) => {
        if(status == 'editing'){
            snapshot()
            status = 'drawing'
            return 
        }

        let svg = e.target as SVGElement

        const {left, top} = svg.getBoundingClientRect()

        let circle: Circle = {
            id:window.crypto.randomUUID(), 
            cx: e.clientX - left,
            cy: e.clientY - top, 
            r:40
        }
        circles.push(circle)
        snapshot()
    }

    const redo = () => {
        circles = snapshots[++history]
    }
    const undo = () => {
        circles = snapshots[--history]
    }
    const snapshot = () => {
        history++;
        snapshots.push($state.snapshot(circles)) //keep track of states of circles lists
    }
</script>

<div class='space-y'>
    <div class='actions flex-center'>
        <button onclick={redo} disabled={history == snapshots.length-1}>Redo</button>
        <button onclick={undo} disabled={history == -1}>Undo</button>
    </div>

    {#if status == 'editing'}
        <div class='adjust surface-2 space-y'>
            <span>Adjust Diameter of Circle at ({selected.cx}, {selected.cy})</span>
            <input type='range' min=1 max=100 bind:value={selected.r}>
        </div>
    {/if}
    <!-- svelte-ignore a11y_click_events_have_key_events -->
    <!-- svelte-ignore a11y_no_static_element_interactions -->
    <svg onclick={drawCircle} viewBox="0 0 600 400">
        {#each circles as circle}
            <circle {...circle} 
            stroke="#fff" 
            fill={selected?.id === circle.id ? "#888" :""} 
            stroke-width=2 
            onclick={
                (e)=>{
                    e.stopPropagation()
                    console.log(circle.id)
                    selected = circle
                }
            }
            oncontextmenu={(e)=>{
                e.preventDefault()
                if(status == 'editing'){
                    snapshot()
                }
                status = 'editing'
                selected=circle
            }}>
            </circle>
        {/each}
    </svg>
</div>

<style>
    .surface-2{
        color: var(--color-1);
        box-shadow: var(--shadow-2);
        background-color: var(--gray-8);
        border-radius: var(--radius-12);
    }
    svg{
        height: 400px;
        width: 600px;
        /* to match the viewbox x1, y1, x2, y2 coordinates to clientX-left or clientY-top centers on the mouse */
        border: 2px solid var(--gray-1)
    }

    button{
        background-color: #111;
    }
    .adjust{
        width: 400px;
        position: absolute;
        left: 50%;
        top: 50%;
        /* transform: translate(-50%, -50%); */
        translate: -50% -50%;
        padding: 8px;
    }
</style>