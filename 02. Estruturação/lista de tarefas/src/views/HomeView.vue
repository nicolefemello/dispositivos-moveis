<script setup>
import { ref } from 'vue';
import TaskItem from '@/components/TaskItem.vue';

const tasks = ref([
    { id: 1, text: 'Estudar Vue.js', completed: false },
    { id: 2, text: 'Fazer exercícios', completed: true },
    { id: 3, text: 'Criar projeto', completed: false },
]);

const newTask = ref('');

function addTask() {
    if (newTask.value.trim()) {
        tasks.value.push({
            id: Date.now(),
            text: newTask.value,
            completed: false,
        });
        newTask.value = '';
    }
}

function toggleTask(id) {
    const task = tasks.value.find((t) => t.id === id);
    if (task) {
        task.completed = !task.completed;
    }
}

function deleteTask(id) {
    tasks.value = tasks.value.filter((t) => t.id !== id);
}
</script>

<template>
    <div class="app">
        <h1>Minhas Tarefas</h1>

        <div class="add-task">
            <input v-model="newTask" @keyup.enter="addTask" placeholder="Nova tarefa..." />
            <button @click="addTask">Adicionar</button>
        </div>

        <div class="tasks">
            <TaskItem v-for="task in tasks" :key="task.id" :id="task.id" :text="task.text" :completed="task.completed"
                @toggle="toggleTask" @delete="deleteTask" />
        </div>
    </div>
</template>

<style scoped>
.app {
    max-width: 600px;
    margin: 0 auto;
    padding: 20px;
}

.add-task {
    display: flex;
    gap: 8px;
    margin-bottom: 20px;
}

.add-task input {
    flex: 1;
    padding: 12px;
    border: 2px solid #ddd;
    border-radius: 8px;
}

.add-task button {
    padding: 12px 24px;
    background: #0b5cff;
    color: white;
    border: none;
    border-radius: 8px;
}
</style>