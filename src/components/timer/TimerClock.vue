<template>
  <div class="timer-box" :style="{ left: `${posX}px`, top: `${posY}px` }">
    <div class="grid-dots">
      <img
        @mousedown="startDragging"
        src="../../assets/icons/timer/grid.svg"
        alt="grid dots"
      />
    </div>
    <div class="timer-clock">
      <label>25:00</label>
    </div>
    <div class="timer-buttons">
      <img src="../../assets/icons/timer/play.svg" alt="play button" />
      <img src="../../assets/icons/timer/pause.svg" alt="pause button" />
      <img src="../../assets/icons/timer/reset.svg" alt="stop button" />
      <img src="../../assets/icons/timer/config.svg" alt="config button" />
    </div>
  </div>
</template>

<script setup>
import { ref, onUnmounted } from "vue";

// Variables reactivas para Drag-Drop
const posX = ref(100); // Posición inicial en X para el grid-dots
const posY = ref(20); // Posición inicial en Y para el grid-dots
const offsetX = ref(0); // Para calcular la distancia
const offsetY = ref(0); // Para calcular la distancia
const isMouseDown = ref(false); // Indicador de si el mouse está presionado

// Estado del Temporizador
const studyTime = ref(25 * 60); // 25 min a segundos
const shortBreakTime = ref(5 * 60); // 5 min a segundos
const longBreakTime = ref(15 * 60); // 15 min a segundos
const cyclesBeforeLongBreak = ref(4); // Cantidad de ciclos
const timeElapsed = ref(studyTime.value);
const interval = ref(undefined);
const currentCycle = ref(0); // Ciclo actual del estudio-descanso
const isStudyTime = ref(true); // Si es tiempo de estudio o de descanso

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
.timer-box {
  position: absolute;
  z-index: 90;
  background-color: var(--clock-dark-mode);
  border: 2px solid var(--lateral-dark-mode);
  border-radius: 10px;
  padding: 1rem;
  align-items: center;
  width: 20rem;
  height: 8rem;
  color: var(--text-dark-mode);
  font-size: 2rem;
  display: grid;
  grid-template-columns: 1fr 1fr 1fr;
  grid-template-rows: auto auto;
  grid-template-areas:
    "grid-dots timer-clock ."
    "timer-buttons timer-buttons timer-buttons";
  box-shadow: 5px 5px 5px #16202e;
}

.grid-dots {
  grid-area: grid-dots;
  display: flex;
  /* justify-self: left; */
  min-height: 100%;
}

.grid-dots img {
  height: 1rem;
  align-items: self-start;
  cursor: grab;
}

.timer-clock {
  grid-area: timer-clock;
  display: flex;
  justify-content: center;
}

.timer-buttons {
  grid-area: timer-buttons;
  display: flex;
  flex-direction: row;
  justify-content: space-evenly;
  justify-self: center;
  height: 2.2rem;
  width: 70%;
}

.timer-buttons img {
  cursor: pointer;
}
</style>
