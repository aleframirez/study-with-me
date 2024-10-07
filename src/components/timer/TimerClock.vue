<template>
  <div>
    <div class="timer-box" :style="{ left: `${posX}px`, top: `${posY}px` }">
      <div class="grid-dots">
        <img
          @mousedown="startDragging"
          src="../../assets/icons/timer/grid.svg"
          alt="grid dots"
        />
      </div>
      <div class="timer-clock">
        <label>{{ formatTime(timeElapsed) }}</label>
      </div>
      <div class="timer-buttons">
        <img
          v-show="startPause"
          @click="start"
          src="../../assets/icons/timer/play.svg"
          alt="play button"
        />
        <img
          v-show="!startPause"
          @click="pause"
          src="../../assets/icons/timer/pause.svg"
          alt="pause button"
        />
        <img
          @click="reset"
          src="../../assets/icons/timer/reset.svg"
          alt="stop button"
        />
        <img
          @click="showConfig"
          src="../../assets/icons/timer/config.svg"
          alt="config button"
        />
      </div>
    </div>

    <TimerConfig
      v-if="showConfigTimer"
      :studyTime="studyTime"
      :shortBreakTime="shortBreakTime"
      :longBreakTime="longBreakTime"
      :cyclesBeforeLongBreak="cyclesBeforeLongBreak"
      @apply-settings="applySettings"
      @close-config="showConfig"
    />
  </div>
</template>

<script setup>
import { ref, onUnmounted } from "vue";
import TimerConfig from "./TimerConfig.vue";

// Variables reactivas para Drag-Drop
const posX = ref(650); // Posición inicial en X para el grid-dots
const posY = ref(50); // Posición inicial en Y para el grid-dots
const offsetX = ref(0); // Para calcular la distancia
const offsetY = ref(0); // Para calcular la distancia
const isMouseDown = ref(false); // Indicador de si el mouse está presionado

// Estado del Temporizador
const studyTime = ref(45 * 60); // 25 min a segundos
const shortBreakTime = ref(15 * 60); // 5 min a segundos
const longBreakTime = ref(30 * 60); // 15 min a segundos
const cyclesBeforeLongBreak = ref(6); // Cantidad de ciclos
const timeElapsed = ref(studyTime.value);
const interval = ref(undefined);
const currentCycle = ref(0); // Ciclo actual del estudio-descanso
const isStudyTime = ref(true); // Si es tiempo de estudio o de descanso
const startPause = ref(true); // Cambiar icono entre Start y Pause
const showConfigTimer = ref(false); // Mostrar la configuracion

// Funcion para mostrar la config
function showConfig() {
  showConfigTimer.value = !showConfigTimer.value;
  console.log(showConfigTimer.value);
}

// Funcion para aplicar los cambios
function applySettings(newSettings) {
  studyTime.value = newSettings.studyTime;
  shortBreakTime.value = newSettings.shortBreakTime;
  longBreakTime.value = newSettings.longBreakTime;
  cyclesBeforeLongBreak.value = newSettings.cyclesBeforeLongBreak;

  // Aplicamos los cambio al timer
  timeElapsed.value = isStudyTime.value
    ? studyTime.value
    : shortBreakTime.value;
  showConfig();
}

// Funcion para cambiar de boton
function switchStartButton() {
  startPause.value = !startPause.value;
}

// Funcion para iniciar el temporizador
function start() {
  if (interval.value) return; // Para evitar multiples inicios del temporizador
  switchStartButton();

  interval.value = setInterval(() => {
    if (timeElapsed.value > 0) {
      timeElapsed.value--;
    } else {
      // Cambiar entre estudio y descanso cuando llega a 0
      pause(); // Pausamos el temporizador actual
      if (isStudyTime.value) {
        currentCycle.value++;
        // Si es tiempo de estudio y hemos completado los ciclos, comenzar descanso largo
        if (currentCycle.value >= cyclesBeforeLongBreak.value) {
          startLongBreak(); // Funcion para el ciclo de descanso largo
        } else {
          startShortBreak(); // Funcion para el ciclo de descanso corto
        }
      } else {
        // Si el descanso largo ha terminado, no reiniciamos automaticamente
        if (timeElapsed.value === 0 && currentCycle.value === 0) {
          pause(); // Fin del ciclo largo
          startPause.value = true;
        } else {
          startStudy(); // Si aun faltan ciclos, volver a estudiar
        }
      }
    }
  }, 1000);
}

// Funcion para pausar el temporizador
function pause() {
  switchStartButton();
  clearInterval(interval.value);
  interval.value = undefined;
}

// Funcion para resetear temporizador y descansos
function reset() {
  pause(); // Pausamos el reloj para que no empiece de una
  startPause.value = true;
  timeElapsed.value = studyTime.value; // Volvemos al valor inicial
  currentCycle.value = 0; // Reiniciamos los ciclos
  isStudyTime.value = true; // Volver al tiempo de estudio
}

// Funcion para comenzar el temp de estudio
function startStudy() {
  timeElapsed.value = studyTime.value;
  isStudyTime.value = true;
  start();
}

// Funcion para comenzar el temp de descanso corto
function startShortBreak() {
  timeElapsed.value = shortBreakTime.value;
  isStudyTime.value = false;
  start();
}

// Funcion para comenzar el temp de descanso largo
function startLongBreak() {
  timeElapsed.value = longBreakTime.value;
  isStudyTime.value = false;
  currentCycle.value = 0; // Reiniciamos el ciclo luego del descanso largo
  start();
}

// Funcion para formatear el tiempo como mm:ss
function formatTime(interval) {
  const minutes = `0${Math.floor(interval / 60)}`.slice(-2);
  const seconds = `0${interval % 60}`.slice(-2);
  return `${minutes}:${seconds}`;
}

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
  user-select: none;
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
