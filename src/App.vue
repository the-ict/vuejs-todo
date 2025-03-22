<template>
    <div class="container">
        <div class="content">
            <div class="content-title">
                <h1>What's up </h1>
                <input type="text" v-model="name" placeholder="Ismingiz" class="content-name__input">
            </div>
            <div class="content-search">
                <label>
                    Qidirish
                </label>
                <div class="content-search__form">
                    <input type="text" placeholder="Qidiring">
                    <i class="fa-solid fa-magnifying-glass search-icon"></i>
                </div>
            </div>
            <form class="content-form content-search__form" @submit.prevent="addTodo">
                <input type="text" placeholder="Nomini kiriting !" v-model="todoName">
                <button type="submit" @click="addTodo">Yaratish</button>
            </form>
            <div class="content-items">
                <label v-if="todos.length > 0">Vazifalaringiz!</label>
                <label v-if="todos.length === 0">Vazifa qo'shing!</label>
                <div class="content-item" v-for="(todo, index) in todos" :key="todo.id" @click="toogleItem(index)">
                    <p :class="`${todo.done && 'passed'}`" v-if="editingIndex !== todo.id">{{ todo.name }}</p>
                    <input type="text" v-else v-model="editingText">
                    <div class="content-item__buttons">
                        <button @click="handleDelete(todo.id)">O'chirish</button>
                        <button @click="handleUpdate(todo.id)">O'zgartirish</button>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script setup>
import { onMounted, ref, watch } from "vue"

const name = ref("")
const search = ref("")
const todoName = ref("")


const editingIndex = ref(null)
const editingText = ref("")


const todos = ref([])

const addTodo = () => {
    if (todoName.value.trim() !== "") {
        todos.value.push({
            id: todos.value.length,
            name: todoName.value,
            done: false
        });
        todoName.value = "";
    }
}


const toogleItem = (item) => {
    todos.value = todos.value.map(todo => {
        if (todo.id === item) {
            return {
                ...todo,
                done: !todo.done
            }
        }
        return todo
    })
}

const handleDelete = (index) => {
    todos.value = todos.value.filter(todo => todo.id !== index)
}

const handleUpdate = (index) => {
    if (editingText.value.trim() === '') {
        editingIndex.value = index
        editingText.value = todos.value[index].name
    } else {
        todos.value[index].name = editingText.value
        editingIndex.value = null
        editingText.value = ""
    }
}


watch(name, (value) => {
    localStorage.setItem("name", value)
})

watch(todos, (newVal) => {
    localStorage.setItem("todos", JSON.stringify(newVal))
}, { deep: true })

onMounted(() => {
    name.value = localStorage.getItem("name") || "";

    const savedTodos = localStorage.getItem("todos");
    todos.value = savedTodos ? JSON.parse(savedTodos) : [];
});

</script>

<style scoped>
.container {
    display: flex;
    justify-content: center;
    font-family: "Roboto", sans-serif;
}

.content {
    width: 600px;
    gap: 10px;
    padding: 10px 0px;
}

.content-title {
    display: flex;
    gap: 10px;
    align-items: end;
    font-size: 10px;
}

.content-title input {
    border: none;
    outline: none;
    font-size: 16px;
}

.content-search {
    margin: 10px 0px;
    display: flex;
    flex-direction: column;
    gap: 10px;
}

.content-form button {
    padding: 10px 20px;
    cursor: pointer;
    border: none;
    outline: none;
}

.content-search__form {
    display: flex;
    align-items: center;
    gap: 10px;
    padding: 3px;
    background-color: #efefef;
    border-radius: 5px;
}

.content-search__form input {
    padding: 10px 5px;
    flex: 1;
    background-color: transparent;
    border: none;
    outline: none;
}

.content-search__form i {
    cursor: pointer;
}

.content-items {
    margin-top: 10px;
}

.content-item {
    margin: 10px 0px;
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 10px 5px;
    background-color: #8ecae6;
    gap: 1rem;
    border-radius: 4px;
    transition: all 200ms ease-in-out;
    cursor: pointer;
    color: white;
    position: relative;
}

.content-item__buttons {
    display: flex;
    gap: 10px;
}

.content-item__buttons button {
    padding: 7px 7px;
    cursor: pointer;
    border: none;
    outline: none;
    border-radius: 3px;
}

.content-item__buttons button:first-child {
    background-color: #ef233c;
    color: white;
}

.content-item:hover {
    background-color: #219ebc;
}

.content-item input {
    flex: 1;
    background: transparent;
    border: none;
    outline: none;
    color: white;
}

.passed {
    text-decoration: line-through;
    color: lightgray;
}
</style>