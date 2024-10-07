<template>
  <section class="todo-list">
    <h3>TODO LIST</h3>

    <div class="list">
      <div
        v-for="todo in todos"
        :key="todo.createdAt"
        :class="`todo-item ${todo.done && 'done'}`"
      >
        <div class="todo-content">
          <input type="text" v-model="todo.content" />
        </div>

        <label>
          <input type="checkbox" v-model="todo.done" />
          <span :class="`bubble ${todo.category}`"></span>
        </label>

        <div class="actions">
          <button class="delete" @click="remove(todo)">Delete</button>
        </div>
      </div>
    </div>
  </section>
</template>

<script setup>
const props = defineProps({
  todos: Array,
});

const emit = defineEmits(["remove-todo"]);

const remove = (todo) => {
  emit("remove-todo", todo);
};
</script>

<style scoped>
/* Quitamos para darle estilos personalizados */
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
  border: 2px solid var(--business-button);
  box-shadow: 0px 0px 4px rgb(255, 179, 71, 0.75);
}

.bubble.personal {
  border-color: var(--personal-button);
  box-shadow: 0px 0px 4px rgb(193, 68, 14, 0.75);
}

.bubble::after {
  content: "";
  display: block;
  opacity: 0;
  width: 0;
  height: 0;
  background-color: var(--business-button);
  box-shadow: 0px 0px 4px rgb(255, 179, 71, 0.75);
  border-radius: 50%;
  transition: 0.2s ease-in-out;
}

.bubble.personal::after {
  background-color: var(--personal-button);
  box-shadow: 0px 0px 4px rgb(255, 179, 71, 0.75);
}

input:checked ~ .bubble::after {
  width: 12px;
  height: 12px;
  opacity: 1;
}

.todo-list {
  margin: 1rem 0;
  width: 100%;
}

.todo-list .todo-item {
  display: flex;
  align-items: center;
  padding: 0.3rem;
  border-bottom: 2px solid var(--lateral-dark-mode);
  border-radius: 10px;
  margin-bottom: 1rem;
}

.todo-item label {
  display: block;
  margin-right: 0.5rem;
  cursor: pointer;
}

.todo-item .todo-content {
  flex: 1 1 0%;
}

.todo-item .todo-content input {
  color: var(--text-dark-mode);
  /* font-size: 1rem; */
}

.todo-item .actions {
  display: flex;
  align-items: center;
}

.todo-item .actions button {
  display: block;
  padding: 0.5rem;
  border-radius: 0.25rem;
  color: #fff;
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
  background-color: var(--important-dark-mode);
}

.todo-item.done .todo-content input {
  text-decoration: line-through;
  color: var(--notes-light-mode);
}
</style>
