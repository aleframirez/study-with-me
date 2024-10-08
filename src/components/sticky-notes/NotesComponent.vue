<template>
  <section :style="{ left: `${posX}px`, top: `${posY}px` }">
    <div class="title-box">
      <img
        @mousedown="startDragging"
        src="../../assets/icons/timer/grid.svg"
        alt="grid icon"
      />
      <input type="text" v-model="editableNote.title" />
      <button @click="deleteNote">x</button>
    </div>
    <textarea
      class="note-content"
      v-model="editableNote.content"
      @input="updateNote"
    ></textarea>
  </section>
</template>

<script setup>
import { onUnmounted, ref } from "vue";

const props = defineProps({
  note: Object,
});
const editableNote = ref({ ...props.note });

// Variables reactivas Drag-Drop
const posX = ref(props.note.positionX); // Posición inicial en X
const posY = ref(props.note.positionY); // Posición inicial en Y
const offsetX = ref(0); // Para calcular la distancia
const offsetY = ref(0); // Para calcular la distancia
const isMouseDown = ref(false); // Indicador de si el mouse esta presionado

const emit = defineEmits(["update-note", "delete-note"]);

// Emit evento para actualizar la nota cuando cambia su contenido
const updateNote = () => {
  emit("update-note", editableNote.value);
};

// Emitir evento para eliminar la nota
const deleteNote = () => {
  emit("delete-note");
};

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
section {
  position: absolute;
  display: flex;
  flex-direction: column;
  width: 13rem;
  height: 13rem;
  overflow: hidden;
  padding: 0.3rem 0.3rem;
  background-color: var(--notes-dark-mode);
  border-radius: 5px;
  border: 2px solid var(--lateral-dark-mode);
  gap: 0.3rem;
  z-index: 80;
  user-select: none;
}

.title-box {
  display: flex;
  flex-direction: row;
  align-items: center;
  justify-items: center;
  justify-content: space-evenly;
  padding: 3px;
  border-bottom: 1px solid var(--lateral-dark-mode);
}

.title-box input {
  color: var(--text-dark-mode);
  text-align: center;
  font-size: 1rem;
  width: 80%;
}

.title-box img {
  width: 1rem;
  cursor: grab;
}

.note-content {
  resize: none;
  padding: 0.3rem;
  width: 100%;
  height: 100%;
  color: var(--text-dark-mode);
  background-color: var(--notes-dark-mode);
  border: 0px;
  line-height: 1.3;
  outline: none;
}

.title-box button {
  background-color: var(--important-dark-mode);
  width: 1rem;
  border-radius: 3px;
  cursor: pointer;
}
</style>
