<script setup>
defineProps({
    id: Number,
    text: String,
    completed: Boolean,
});

const emit = defineEmits(['toggle', 'delete']);
</script>

<template>
    <div class="task-item" :class="{ completed }">
        <input type="checkbox" :checked="completed" @change="emit('toggle', id)" />
        <span class="text">{{ text }}</span>
        <button @click="emit('delete', id)" class="btn-delete">🗑️</button>
    </div>
</template>

<style scoped>
.task-item {
    display: flex;
    align-items: center;
    gap: 12px;
    padding: 12px;
    background: white;
    border-radius: 8px;
    margin-bottom: 8px;
}

.task-item.completed .text {
    text-decoration: line-through;
    color: #999;
}

.text {
    flex: 1;
}

.btn-delete {
    background: none;
    border: none;
    font-size: 20px;
    cursor: pointer;
}
</style>