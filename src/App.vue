<template>
  <section>
    <h1 style="display: none">Study With Me</h1>
    <section>
      <aside
        class="draggable-window"
        :style="{ left: `${posX}px`, top: `${posY}px` }"
      >
        <img
          @mousedown="startDragging"
          class="header"
          src="./assets/icons/aside/grip.svg"
          alt="grip"
        />
        <img
          @click="showTimer"
          class="img-space"
          src="./assets/icons/aside/timer.svg"
          alt="timer"
        />
        <img
          @click="showTodo"
          class="img-space"
          src="./assets/icons/aside/todo.svg"
          alt="todo"
        />
        <img
          @click="showNotes"
          class="img-space"
          src="./assets/icons/aside/notes.svg"
          alt="notes"
        />
        <img
          class="img-space"
          src="./assets/icons/aside/sunmoon.svg"
          alt="sun moon mode"
        />
      </aside>
    </section>
    <section>
      <TimerClock v-show="timerOn" />
      <TodoComponent v-show="todoOn" />
    </section>
  </section>
</template>

<script setup>
import { ref, onUnmounted } from "vue";
import TimerClock from "./components/timer/TimerClock.vue";
import TodoComponent from "./components/todo/TodoComponent.vue";

// Variables reactivas Drag-Drop
const posX = ref(10); // Posición inicial en X
const posY = ref(150); // Posición inicial en Y
const offsetX = ref(0); // Para calcular la distancia
const offsetY = ref(0); // Para calcular la distancia
const isMouseDown = ref(false); // Indicador de si el mouse esta presionado
// Mostrar las distintas ventanas
const timerOn = ref(false);
const todoOn = ref(false);
const notesOn = ref(false);

// Funcion para mostrar el Timer
function showTimer() {
  timerOn.value = !timerOn.value;
}

// Funcion para mostrar el todoList
function showTodo() {
  todoOn.value = !todoOn.value;
}

// Funcion para mostrar las notas
function showNotes() {
  notesOn.value = !notesOn.value;
}

// Funcion para iniciar el arrastre
const startDragging = (event) => {
  event.preventDefault(); // Prevenir comportamientos no deseados

  if (event.button === 0) {
    // Si el boton izuierdo esta presionado, hacemos lo siguiente
    isMouseDown.value = true;
    // Calculamos el offset entre la posicion del mouse y la posicion de la ventana
    offsetX.value = event.clientX - posX.value;
    offsetY.value = event.clientY - posY.value;

    // Escuchar el evento de movimiento del mouse
    document.addEventListener("mousemove", drag);
    document.addEventListener("mouseup", stopDragging);
  }
};

// Funcion para arrastrar el elemento
const drag = (event) => {
  event.preventDefault(); // Prevenir el comportamiento predeterminado

  if (isMouseDown.value) {
    // Vamos actualizando la posicion del elemento
    posX.value = event.clientX - offsetX.value;
    posY.value = event.clientY - offsetY.value;
  }
};

// Funcion para detener el arrastre
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
.draggable-window {
  position: absolute;
  display: flex;
  flex-direction: column;
  width: 3rem;
  min-height: 30vh;
  overflow: hidden;
  padding: 1rem 0.3rem;
  background-color: var(--lateral-dark-mode);
  border-radius: 5px;
  border: 2px solid var(--clock-dark-mode);
  gap: 1rem;
  z-index: 100;
  user-select: none;
}

.header {
  cursor: grab;
}

.img-space {
  cursor: pointer;
}
</style>
