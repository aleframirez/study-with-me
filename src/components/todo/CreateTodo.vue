<template>
  <section class="create-todo">
    <h3>Create new task</h3>

    <form @submit.prevent="addTodo">
      <h4>What's your schedule for today?</h4>

      <input
        type="text"
        name="content"
        placeholder="e.g. Buy some bread"
        v-model="input_content"
      />

      <h4>Pick a category</h4>
      <div class="options">
        <label>
          <input
            type="radio"
            name="category"
            value="business"
            v-model="input_category"
          />
          <span class="bubble business"></span>
          <div>Business</div>
        </label>

        <label>
          <input
            type="radio"
            name="category"
            value="personal"
            v-model="input_category"
          />
          <span class="bubble personal"></span>
          <div>Personal</div>
        </label>
      </div>

      <input type="submit" value="Add task" />
    </form>
  </section>
</template>

<script setup>
import { ref } from "vue";

const input_content = ref("");
const input_category = ref(null);

const emit = defineEmits(["add-todo"]);

// Funcion para crear tarea
const addTodo = () => {
  // Si el input esta vacio o no se selecciono categoria no hacer nada
  if (input_content.value.trim() === "" || !input_category.value) return;

  // Emitimos un evento con la nueva tarea
  const newTodo = {
    content: input_content.value,
    category: input_category.value,
    done: false,
    editable: false,
    createdAt: new Date().getTime(),
  };

  // Emitir evento a componente padre
  emit("add-todo", newTodo);

  // Reseteamos los inputs
  input_content.value = "";
  input_category.value = null;
};
</script>

<style scoped>
section {
  margin: 1.5rem 0;
  padding: 0 1rem;
  width: 100%;
}

h3 {
  font-size: 1rem;
  font-weight: 400;
  margin-bottom: 0.5rem;
}

h4 {
  font-size: 0.875rem;
  font-weight: 700;
  margin-bottom: 0.5rem;
}

.create-todo input[type="text"] {
  display: block;
  width: 100%;
  font-size: 1.125rem;
  padding: 0.5rem 1.5rem;
  background-color: var(--notes-dark-mode);
  box-shadow: 2px 2px 2px black;
  border-radius: 10px;
  margin-bottom: 1.5rem;
}

.options {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  grid-gap: 1rem;
  margin-bottom: 1.5rem;
}

.options label {
  display: flex;
  flex-direction: row;
  align-items: center;
  justify-content: space-evenly;
  padding: 1rem;
  background-color: var(--notes-dark-mode);
  border-radius: 10px;
  box-shadow: 2px 2px 2px black;
  cursor: pointer;
}

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

.options label div {
  font-size: 1.125rem;
  /* margin-top: 1rem; */
}

.create-todo input[type="submit"] {
  display: block;
  width: 100%;
  font-size: 1.125rem;
  padding: 1rem 1.5rem;
  color: #fff;
  background-color: var(--important-dark-mode);
  border-radius: 0.5rem;
  box-shadow: 0px 0px 4px rgb(255, 179, 71, 0.75);
  cursor: pointer;
  transition: 0.2s ease-in-out;
}

.create-todo input[type="submit"]:hover {
  opacity: 0.75;
}
</style>
