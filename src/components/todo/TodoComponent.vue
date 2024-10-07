<template>
  <main class="todo-box" :style="{ left: `${posX}px`, top: `${posY}px` }">
    <section class="greeting">
      <img
        @mousedown="startDragging"
        src="../../assets/icons/timer/grid.svg"
        alt="grid icon"
      />
      <h2 class="title">
        What's up,
        <input type="text" placeholder="Name Here!" v-model="name" />
      </h2>
    </section>

    <CreateTodo @add-todo="addTodo" />

    <TodoList :todos="todos_asc" @remove-todo="removeTodo" />
  </main>
</template>

<script setup>
import { computed, onMounted, onUnmounted, ref, watch } from "vue";
import CreateTodo from "./CreateTodo.vue";
import TodoList from "./TodoList.vue";

// Variables reactivas para Drag-Drop
const posX = ref(100); // Posición inicial en X para el grid-dots
const posY = ref(50); // Posición inicial en Y para el grid-dots
const offsetX = ref(0); // Para calcular la distancia
const offsetY = ref(0); // Para calcular la distancia
const isMouseDown = ref(false); // Indicador de si el mouse está presionado

const todos = ref([]); // Array que contendra todas las tareas
const name = ref(""); // Nombre del usuario

// Funcion para ordenar las tareas de mas nuevas a mas antiguas
const todos_asc = computed(() =>
  todos.value.sort((a, b) => {
    return b.createdAt - a.createdAt;
  })
);

// Funcion para agregar una nueva tarea
const addTodo = (todo) => {
  todos.value.push(todo);
  console.log(todos.value);
};

// Funcion para eliminar una tarea
const removeTodo = (todo) => {
  todos.value = todos.value.filter((t) => t !== todo);
};

// Guardar los datos en el localStorage
watch(
  todos,
  (newVal) => {
    localStorage.setItem("todos", JSON.stringify(newVal));
  },
  { deep: true }
);

// Esperar cambios en el nombre del Usuario
watch(name, (newVal) => {
  localStorage.setItem("name", newVal);
});

// Mostrar valores al iniciar el componente
onMounted(() => {
  name.value = localStorage.getItem("name") || "";
  todos.value = JSON.parse(localStorage.getItem("todos")) || [];
});

// Función para iniciar el arrastre de grid-dots
const startDragging = (event) => {
  event.preventDefault(); // Prevenir comportamientos no deseados

  if (event.button === 0) {
    isMouseDown.value = true;
    // Calculamos el offset entre la posición del mouse y la posición de grid-dots
    offsetX.value = event.clientX - posX.value;
    offsetY.value = event.clientY - posY.value;

    // Escuchar el evento de movimiento del ratón
    document.addEventListener("mousemove", drag);
    document.addEventListener("mouseup", stopDragging);
  }
};

// Función para arrastrar el grid-dots
const drag = (event) => {
  event.preventDefault(); // Prevenir el comportamiento predeterminado

  if (isMouseDown.value) {
    // Vamos actualizando la posición del grid-dots
    posX.value = event.clientX - offsetX.value;
    posY.value = event.clientY - offsetY.value;
  }
};

// Función para detener el arrastre de grid-dots
const stopDragging = () => {
  isMouseDown.value = false; // Desactivamos el estado de presionado
  // Removemos los eventos
  document.removeEventListener("mousemove", drag);
  document.removeEventListener("mouseup", stopDragging);
};

// Limpiar eventos al desmontar el componente
onUnmounted(() => {
  document.removeEventListener("mousemove", drag);
  document.removeEventListener("mouseup", stopDragging);
});
</script>

<style scoped>
.todo-box {
  position: absolute;
  z-index: 80;
  background-color: var(--clock-dark-mode);
  border: 2px solid var(--lateral-dark-mode);
  border-radius: 10px;
  padding: 1rem;
  width: 25rem;
  color: var(--text-dark-mode);
  box-shadow: 5px 5px 5px #16202e;
  display: flex;
  flex-direction: column;
  align-items: start;
  user-select: none;
}

.greeting {
  display: flex;
  align-items: center;
}

.greeting img {
  width: 1rem;
  margin-right: 1rem;
  cursor: grab;
}

.greeting .title {
  display: flex;
}

.greeting .title input {
  margin-left: 0.5rem;
  flex: 1 1 0%;
  min-width: 0;
  max-width: 14rem;
}

.greeting .title,
.greeting .title input {
  color: var(--dark);
  font-size: 1.5rem;
  font-weight: 700;
}
</style>
