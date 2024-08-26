
<script lang='ts'>
	import { onMount } from "svelte";
    import type { TodoItem } from "./types";

    let TodoList: TodoItem[] = []
    let newTaskName: string = ""

    async function getTodos() {
        const response = await fetch("http://localhost:3000/get-todos")
        if(!response.ok){
            console.log('failed to get todo list with response:: '+response.status)
            throw new Error('get todos failed')
        }

        TodoList = await response.json()

        console.log('::to do list updated::\n' + JSON.stringify(TodoList))
        
    }

    async function addTask(taskName: string) {

        if(taskName != '') {
            console.log('adding todo of name:: '+taskName)
            const response = await fetch("http://localhost:3000/add-todo", {
                method: 'POST',
                body: JSON.stringify({name: taskName}),
                headers: {
                    "Content-Type": "application/json",
                },
            });

            if(!response.ok){
                console.log("add task failed:: "+response.status)
                throw new Error("add task failed");
            }

            //  get the list to still update, without needing to replace the list

            getTodos()

            console.log('todo added of name:: '+taskName)
        }else{
            console.log('task name is blank.  adding nothing')
        }
        console.log('::todoList::')
        console.log(JSON.stringify(TodoList))
    }

    async function deleteTask(taskid: number) {
        console.log('deleting task id:: '+taskid)
    
        const response = await fetch("http://localhost:3000/remove-todo", {
            method: "POST",
            body: JSON.stringify({id: taskid}),
            headers: {
                "Content-Type":"application/json"
            }
        })

        if(!response.ok) {
            console.log('remove task failed:: '+response.status)
            throw new Error('remove task failed')
        }

        getTodos()

        console.log('removed todo of id:: '+taskid)
    }

    async function markAsDone(taskid: number) {
        console.log('marking as done task of id:: '+taskid)

        const response = await fetch("http://localhost:3000/tag-task-as-complete", {
            method: 'POST',
            body: JSON.stringify({id: taskid}),
            headers: {
                'Content-Type':'application/json'
            }
        })

        if(!response.ok){
            console.log('failed to mark as complete task of id:: '+taskid+'\nfailed with response code:: '+response.status)
            throw new Error('failed to tag as complete')
        }

        getTodos()

        console.log('sucsessfully marked as done task of id:: '+taskid)
    }
    async function markAsIncomplete(taskid: number) {
        console.log('marking as done task of id:: '+taskid)

        const response = await fetch("http://localhost:3000/tag-task-as-incomplete", {
            method: 'POST',
            body: JSON.stringify({id: taskid}),
            headers: {
                'Content-Type':'application/json'
            }
        })

        if(!response.ok){
            console.log('failed to mark as incomplete task of id:: '+taskid+'\nfailed with response code:: '+response.status)
            throw new Error('failed to tag as incomplete')
        }

        getTodos()

        console.log('sucsessfully marked as incomplete task of id:: '+taskid)
    }

    onMount(() => {
        getTodos()
    })
</script>

<head>
    <style>
        h1 {text-align: center}
        h2 {text-align: center}
        div {
            display: flex
            }
    </style>
</head>

<body>
    <h1>POGGERS To-do list</h1>
    <div style= 
        "
        padding: 5px;

        border: 2px solid black; background-color: #79286C;
        border-radius:10px;
        "
    >
        <div style="
                flex-direction: column;

                padding: inherit;
                border: 2px solid black; background-color: #FFA500;
                border-radius: 10px;
        ">
            <h2>Add new task</h2>
            <div>
                <input bind:value={newTaskName} type='text' placeholder='add new task'>
                <button on:click={() => {addTask(newTaskName)}}>=></button>
        
            </div>
        </div>
        <div style=
                "
                width: 100%;
                justify-content: center;
                flex-direction: column;

                padding: inherit;
                border: 2px solid black; background-color: #ADD8E6;
                border-radius:10px;
                "
        >
            <h2>TASKS</h2>
            {#each TodoList as task}
                {#if task.isDone}
                    <div style="
                    padding: inherit;
                    border: 2px solid black; background-color: #00FF1C;
                    border-radius: 10px;
                    padding: inherit;
                    ">
                        <input bind:checked={task.isDone} type="checkbox">
                        {task.name}
                        <button style="margin-left: auto" on:click={() => deleteTask(task.id)}>remove task</button>
                    </div>
                {:else}
                    <div style="
                    padding: inherit;
                    border: 2px solid black; background-color: #FF0000;
                    border-radius: 10px;
                    padding: inherit;
                    ">
                        <input bind:checked={task.isDone} type="checkbox">
                        {task.name}
                        <button style="margin-left: auto" on:click={() => deleteTask(task.id)}>remove task</button>
                    </div>
                {/if}
            {/each}
        </div>
    </div>
</body>