<template>
  <section class="timer-config-box">
    <div class="config-box-content">
      <label>
        Study Time (minutes):
        <input
          type="number"
          v-model.number="localStudyTime"
          @input="validateStudyTime"
        />
      </label>
      <span v-if="errors.studyTime">{{ errors.studyTime }}</span>

      <label>
        Short Break (minutes):
        <input
          type="number"
          v-model.number="localShortBreakTime"
          @input="validateShortBreakTime"
        />
      </label>
      <span v-if="errors.shortBreakTime">{{ errors.shortBreakTime }}</span>

      <label>
        Long Break (minutes):
        <input
          type="number"
          v-model.number="localLongBreakTime"
          @input="validateLongBreakTime"
        />
      </label>
      <span v-if="errors.longBreakTime">{{ errors.longBreakTime }}</span>

      <label>
        Cycles Before Long Break:
        <input
          type="number"
          v-model.number="localCyclesBeforeLongBreak"
          @input="validateCycles"
        />
      </label>
      <span v-if="errors.cycles">{{ errors.cycles }}</span>

      <div class="config-buttons">
        <label @click="applyChanges">Apply</label>
        <label @click="showConfig">Close</label>
      </div>
    </div>
  </section>
</template>

<script setup>
import { computed, ref } from "vue";

// Van primero las props antes de los estados para que no de error
const props = defineProps({
  studyTime: Number,
  shortBreakTime: Number,
  longBreakTime: Number,
  cyclesBeforeLongBreak: Number,
});

const emit = defineEmits(["apply-settings", "close-config"]);

// Estados locales de componente config
const localStudyTime = ref(props.studyTime / 60);
const localShortBreakTime = ref(props.shortBreakTime / 60);
const localLongBreakTime = ref(props.longBreakTime / 60);
const localCyclesBeforeLongBreak = ref(props.cyclesBeforeLongBreak);

// Estado de errores
const errors = ref({
  studyTime: null,
  shortBreakTime: null,
  longBreakTime: null,
  cycles: null,
});

// Funcion de validacion para timepo de estudio
function validateStudyTime() {
  if (localStudyTime.value <= 0 || isNaN(localStudyTime.value)) {
    errors.value.studyTime = "Study time must be a positive number.";
  } else {
    errors.value.studyTime = null;
  }
}

// Funcion de validacion para timepo de descanso corto
function validateShortBreakTime() {
  if (localShortBreakTime.value <= 0 || isNaN(localShortBreakTime.value)) {
    errors.value.shortBreakTime = "Short Break time must be a positive number";
  } else {
    errors.value.shortBreakTime = null;
  }
}

// Funcion de validacion para timepo de descanso largo
function validateLongBreakTime() {
  if (localLongBreakTime.value <= 0 || isNaN(localLongBreakTime.value)) {
    errors.value.longBreakTime = "Long Break time must be a positive number";
  } else {
    errors.value.longBreakTime = null;
  }
}

// Funcion de validacion para contidad de ciclos
function validateCycles() {
  if (
    localCyclesBeforeLongBreak.value < 1 ||
    isNaN(localCyclesBeforeLongBreak.value)
  ) {
    errors.value.cycles = "Cycles must be at least 1.";
  } else {
    errors.value.cycles = null;
  }
}

// Revisamos que no haya arrores
const hasErrors = computed(() => {
  return (
    errors.value.studyTime ||
    errors.value.shortBreakTime ||
    errors.value.longBreakTime ||
    errors.value.cycles
  );
});

// Si todo esta correcto aplicamos los cambios
function applyChanges() {
  validateStudyTime();
  validateShortBreakTime();
  validateLongBreakTime();
  validateCycles();

  if (!hasErrors.value) {
    emit("apply-settings", {
      studyTime: localStudyTime.value * 60,
      shortBreakTime: localShortBreakTime.value * 60,
      longBreakTime: localLongBreakTime.value * 60,
      cyclesBeforeLongBreak: localCyclesBeforeLongBreak.value,
    });
    showConfig();
    showConfig();
  }
}

function showConfig() {
  emit("close-config");
}
</script>

<style scoped>
.timer-config-box {
  background-color: rgba(0, 0, 0, 0.8);
  color: var(--text-dark-mode);
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 110;
  user-select: none;
}

.config-box-content {
  min-width: 25rem;
  background-color: var(--clock-dark-mode);
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 2rem 2rem 1rem 2rem;
  border: 2px solid var(--lateral-dark-mode);
  border-radius: 10px;
  gap: 0.3rem;
  font-size: 1.1rem;
  box-shadow: 15px 15px 8px #16202e;
}

.config-box-content label input {
  background-color: transparent;
  color: var(--text-dark-mode);
  font-size: 1.1rem;
  width: 3.5rem;
  padding: 0.1rem 0.1rem 0.1rem 0.3rem;
  border-left: 3px solid var(--lateral-dark-mode);
  border-bottom: 2px solid var(--lateral-dark-mode);
  border-top: 0;
  border-right: 0;
  border-radius: 3px;
  outline: none;
}

.config-box-content span {
  padding-bottom: 0.8rem;
  font-size: 0.8rem;
  color: var(--important-dark-mode);
}

.config-buttons {
  width: 50%;
  display: flex;
  justify-content: space-around;
}

.config-buttons label {
  cursor: pointer;
  padding: 0.5rem;
  border: 2px solid transparent;
}

.config-buttons label:hover {
  cursor: pointer;
  border-radius: 5px;
  border: 2px solid var(--lateral-dark-mode);
  /* color: var(--text-light-mode); */
  background-color: #ffc75794;
}
</style>
