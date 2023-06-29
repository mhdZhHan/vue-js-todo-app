<script setup>
import { onMounted, computed, ref, watch } from 'vue'

const todoItems = ref([])
const name = ref('')

const inputContent = ref('')
const inputCategory = ref(null)

const addTodo = () => {
    if(inputContent.value === '' || inputCategory.value === null) return

    todoItems.value.push({
        taskTitle: inputContent.value,
        isCompleted: false,
        category: inputCategory,
        createdAt: new Date().getTime()
    })
}

const deleteTask = (task) => {
    todoItems.value = todoItems.value.filter((item) => item !== task)
}

// arrange todo items into ascending order
const tasksAsc = computed(() => todoItems.value.sort((a, b) => {
    return b.createdAt - a.createdAt
}))

watch(todoItems, (newValue) => {
    localStorage.setItem('todo_items', JSON.stringify(newValue))
}, { deep: true })

watch(name, (newValue) => {
    localStorage.setItem('user', newValue)
})

onMounted(() => {
    name.value = localStorage.getItem('user') || ''
    todoItems.value = JSON.parse(localStorage.getItem('todo_items')) || []
})
</script>


<template>
    <div class="app">
        <div class="greeting container">
            <h1 class="title">What's up 
                <input 
                    type="text" 
                    placeholder="Name here"
                    v-model="name"
                />
            </h1>
        </div>

        <div class="create-todo container">
            <h3>CREATE A TODO</h3>

            <form @submit.prevent="addTodo()">
                <h4>What's on your todo list?</h4>

                <input 
                    type="text"
                    placeholder="e.g. Learn vue js"
                    v-model="inputContent"
                />
                
                <h4>Pick a category</h4>

                <div class="options">
                    <label for="category_01">
                        <input 
                            type="radio"
                            name="category"
                            id="category_01"
                            value="important"
                            v-model="inputCategory"
                        />
                        <span class="bubble important"></span>
                        <div>Important</div>
                    </label>

                    <label for="category_02">
                        <input 
                            type="radio"
                            name="category"
                            id="category_02"
                            value="personal"
                            v-model="inputCategory"
                        />
                        <span class="bubble personal"></span>
                        <div>Personal</div>
                    </label>
                </div>

                <input type="submit" value="Add todo" />
            </form>
        </div>

        <div class="todo-list container">
            <h3>TASKS LIST</h3>
            <div class="list">
				<div 
                    v-for="task in tasksAsc" 
                    :class="`todo-item ${task.isCompleted && 'done'}`"
                >
					<label>
						<input type="checkbox" v-model="task.isCompleted" />
						<span :class="`bubble ${
							task.category == 'important' 
								? 'important' 
								: 'personal'
						}`"></span>
					</label>

					<div class="todo-content">
						<input type="text" v-model="task.taskTitle" />
					</div>

					<div class="actions">
						<button class="delete" @click="deleteTask(task)">Delete</button>
					</div>
				</div>
            </div>
        </div>
    </div>
</template>

<style scoped>
    .greeting .title {
        display: flex;
    }

    .greeting .title input {
        margin-left: 0.5rem;
        flex: 1 1 0%;
        min-width: 0;
    }

    .greeting .title,
    .greeting .title input {
        color: var(--dark);
        font-size: 1.5rem;
        font-weight: 700;
    }

    .create-todo input[type="text"] {
        display: block;
        width: 100%;
        font-size: 1.125rem;
        padding: 1rem 1.5rem;
        color: var(--dark);
        background-color: #FFF;
        border-radius: 0.5rem;
        box-shadow: var(--shadow);
        margin-bottom: 1.5rem;
    }

    .create-todo .options {
        display: grid;
        grid-template-columns: repeat(2, 1fr);
        grid-gap: 1rem;
        margin-bottom: 1.5rem;
    }

    .create-todo .options label {
        display: flex;
        flex-direction: column;
        align-items: center;
        justify-content: center;
        padding: 1.5rem;
        background-color: #FFF;
        border-radius: 0.5rem;
        box-shadow: var(--shadow);
        cursor: pointer;
    }

    input[type="radio"],
    input[type="checkbox"] {
        display: none;
    }

    .bubble {
        display: flex;
        align-items: center;
        justify-content: center;
        width: 20px;
        height: 20px;
        border-radius: 50%;
        border: 2px solid var(--important);
        box-shadow: var(--important-glow);
    }

    .bubble.personal {
        border-color: var(--personal);
        box-shadow: var(--personal-glow);
    }

    .bubble::after {
        content: "";
        display: block;
        opacity: 0;
        width: 0px;
        height: 0px;
        background-color: var(--important);
        box-shadow: var(--important-glow);
        border-radius: 50%;
        transition: 0.2s ease-in-out;
    }

    .bubble.personal::after {
        background-color: var(--personal);
        box-shadow: var(--personal-glow);
    }

    input:checked~.bubble::after {
        width: 10px;
        height: 10px;
        opacity: 1;
    }

    .create-todo .options label div {
        color: var(--dark);
        font-size: 1.125rem;
        margin-top: 1rem;
    }

    .create-todo input[type="submit"] {
        display: block;
        width: 100%;
        font-size: 1.125rem;
        padding: 1rem 1.5rem;
        color: #FFF;
        background-color: var(--primary);
        border-radius: 0.5rem;
        box-shadow: var(--personal-glow);
        cursor: pointer;
        transition: 0.2s ease-in-out;
    }

    .create-todo input[type="submit"]:hover {
        opacity: 0.88;
    }

    .todo-list .list {
        margin: 1rem 0;
    }

    .todo-list .todo-item {
        display: flex;
        align-items: center;
        background-color: #FFF;
        padding: 1rem;
        border-radius: 0.5rem;
        box-shadow: var(--shadow);
        margin-bottom: 1rem;
    }

    .todo-item label {
        display: block;
        margin-right: 1rem;
        cursor: pointer;
    }

    .todo-item .todo-content {
        flex: 1 1 0%;
    }

    .todo-item .todo-content input {
        color: var(--dark);
        font-size: 1.125rem;
    }

    .todo-item .actions {
        display: flex;
        align-items: center;
    }

    .todo-item .actions button {
        display: block;
        padding: 0.5rem;
        border-radius: 0.25rem;
        color: #FFF;
        cursor: pointer;
        transition: 0.2s ease-in-out;
    }

    .todo-item .actions button:hover {
        opacity: 0.75;
    }

    .todo-item .actions .edit {
        margin-right: 0.5rem;
        background-color: var(--primary);
    }

    .todo-item .actions .delete {
        background-color: var(--danger);
    }

    .todo-item.done .todo-content input {
        text-decoration: line-through;
        color: var(--grey);
    }
</style>