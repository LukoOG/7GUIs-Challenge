<script lang='ts'>

    type Person = {firstname: string, lastname: string,}

    let people = $state<Person[]>([
        {firstname:"John", lastname:"Doe"},
        {firstname:"Pedro", lastname:"Silva"},
        {firstname:"Ben", lastname:"Grayson"},
    ])

    let selected = $state<Person>()
    let person: Person = $state({firstname: "", lastname: ""})

    let search = $state('')
    let filteredPeople = $derived(
        search ? people.filter(
        (p) => p.lastname.toLocaleLowerCase().startsWith(search.toLocaleLowerCase()) ? p : ""
    ) : people
    )

    //update selected whenever the inputs change
    $effect(() => {
        // person.firstname = selected?.firstname ?? ""
        // person.lastname = selected?.lastname ?? "" 
        // causes person to update twice

        person = {
            firstname: selected?.firstname ?? "",
            lastname: selected?.lastname ?? ""
        }
    })
    const createPerson = () => {
        people.push(person)
        clearFields()
    }

    const updatePerson = () => {
        let index = people.indexOf(selected!)
        people[index]= person
        clearFields()
    }

    const deletePerson = () => {
        people = people.filter((p) => {
            return p.firstname !== selected?.firstname || p.lastname !== selected?.lastname
        })
        clearFields()
    }

    const clearFields = () => {
        person = {firstname: "", lastname:""}
        search = ""
    }

    $inspect(people)
</script>

<div class='container'>
    <div class='search'>
        <label class='group'>
            <span>Filtered prefix</span>
            <input type="text" bind:value={search}>
        </label>
    </div>

    <div class='people'>
        <span class=''>Names: </span>
        <select bind:value={selected} size=5 style:min-height={"50px"}>
            {#each filteredPeople as person}
                <option value={person}>{person.firstname}, {person.lastname}</option>
            {/each}
        </select>
    </div>    

    <div class='details'>
        <label class='group'>
            <span>Name: </span>
            <input type="text" bind:value={person.firstname}>
        </label>
        <label class='group'>
            <span>Surname: </span>
            <input type="text" bind:value={person.lastname}>
        </label>
    </div>

    <div class='actions'>
        <button onclick={createPerson}>Create</button>
        <button onclick={updatePerson}>Update</button>
        <button onclick={deletePerson}>Delete</button>
    </div>
</div>

<style>
    input{
        outline: none;
        text-decoration: none;
        margin: 2px auto;
    }
    button{
        text-decoration: none;
        background-color: #111;
        outline: none;
        border: none;
    }

    .container{
        max-width: 500px;
        border: #fdf 1px solid;
        display: grid;
        /* margin: 0 auto; */
        place-content: center;
        grid-template-areas: 
        'search .'
        'peoples details'
        'actions actions';
        grid-template-columns: 1fr 1fr;
        grid-template-rows: auto;
        grid-gap: var(--size-3);
        padding: 1.5rem;
        .search{
            grid-area: search;
            .group{
                display: grid;
                grid-template-columns: 2fr 2fr;
                align-items: baseline;
                grid-template-rows: auto;
            }
        }
        .people{
            min-height: 1.5rem;
            grid-area: peoples;
        }
        .details{
            grid-area: details;
            display: grid;
            grid-template-columns: auto 1fr;
            grid-template-rows: repeat(2, auto) 1px; 
            /* auto auto 1px or auto auto or 1fr auto or so also works */
            grid-gap: 7px;
            padding: 4px;
            align-items: end;
            .group{
                display: contents;
            }
        }
        .actions{
            justify-content: space-around;
            grid-area: actions;
        }
    }
</style>