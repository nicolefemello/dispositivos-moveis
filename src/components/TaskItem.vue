<template>
  <div class="task-item" :class="{ done: task.done }">
    <input
      class="task-checkbox"
      type="checkbox"
      :checked="task.done"
      :aria-label="task.done ? 'Marcar tarefa como pendente' : 'Marcar tarefa como concluída'"
      @change="$emit('toggle', task.id)"
    />
    <img v-if="task.img_url" :src="task.img_url" class="task-thumbnail" alt="" />
    <div class="task-content">
      <span class="task-title">{{ task.title }}</span>
      <span v-if="task.location_label" class="task-location">
        <span class="location-pin" aria-hidden="true">⌖</span>
        {{ task.location_label }}
      </span>
      <span v-else-if="hasCoordinates" class="task-location">
        <span class="location-pin" aria-hidden="true">⌖</span>
        {{ formattedCoordinates }}
      </span>
      <span v-else class="task-location muted">Sem localização</span>
    </div>
    <div class="task-actions" aria-label="Ações da tarefa">
      <button class="task-edit" type="button" @click="$emit('edit', task)">Editar</button>
      <button class="task-remove" type="button" @click="$emit('remove', task.id)">Remover</button>
    </div>
  </div>
</template>

<script setup>
import { computed } from 'vue'

const props = defineProps({
  task: {
    type: Object,
    required: true,
  },
})

const hasCoordinates = computed(() => props.task.latitude != null && props.task.longitude != null)
const formattedCoordinates = computed(
  () => `${Number(props.task.latitude).toFixed(4)}, ${Number(props.task.longitude).toFixed(4)}`,
)

defineEmits(['toggle', 'remove', 'edit'])
</script>

<style scoped>
.task-item {
  display: flex;
  align-items: center;
  gap: 12px;
  padding: 14px;
  margin-bottom: 10px;
  background: #fff;
  border: 1px solid #e6eaf0;
  border-radius: 10px;
  box-shadow: 0 3px 10px rgba(27, 49, 77, 0.06);
  transition:
    opacity 0.2s,
    border-color 0.2s,
    transform 0.2s;
}

.task-item:hover {
  border-color: #b9d2ec;
  transform: translateY(-1px);
}

.task-thumbnail {
  width: 44px;
  height: 44px;
  object-fit: cover;
  border-radius: 6px;
  border: 1px solid #eee;
  flex-shrink: 0;
}

.task-item.done {
  opacity: 0.6;
}

.task-checkbox {
  width: 20px;
  height: 20px;
  accent-color: #4a90d9;
  flex: 0 0 auto;
  cursor: pointer;
}

.task-content {
  display: flex;
  flex: 1;
  min-width: 0;
  flex-direction: column;
  gap: 5px;
}

.task-title {
  overflow: hidden;
  color: #1f2937;
  font-size: 1rem;
  font-weight: 600;
  text-overflow: ellipsis;
  white-space: nowrap;
}

.task-location {
  overflow: hidden;
  color: #55708e;
  font-size: 0.8rem;
  text-overflow: ellipsis;
  white-space: nowrap;
}

.task-location.muted {
  color: #9aa6b2;
}

.location-pin {
  color: #4a90d9;
  font-size: 1rem;
}

.task-item.done .task-title {
  text-decoration: line-through;
  color: #999;
}

.task-remove {
  color: #e74c3c;
}

.task-actions {
  display: flex;
  gap: 4px;
  align-items: center;
  flex: 0 0 auto;
}

.task-edit,
.task-remove {
  background: none;
  border: none;
  cursor: pointer;
  font-size: 0.85rem;
  padding: 4px 8px;
}

.task-edit {
  color: #357abd;
}

.task-edit:hover,
.task-remove:hover {
  text-decoration: underline;
}

@media (max-width: 420px) {
  .task-item {
    align-items: flex-start;
  }

  .task-actions {
    flex-direction: column;
    align-items: flex-end;
  }
}
</style>
