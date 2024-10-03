<template>
  <div class="timer-box">
    <div class="grid-dots">
      <img src="../../assets/icons/timer/grid.svg" alt="grid dots" />
    </div>
    <div class="timer-clock">
      <label>25:00</label>
    </div>
    <div class="timer-buttons">
      <!-- <img src="../../assets/icons/timer/play.svg" alt="play button" /> -->
      <img src="../../assets/icons/timer/pause.svg" alt="pause button" />
      <img src="../../assets/icons/timer/reset.svg" alt="stop button" />
      <img src="../../assets/icons/timer/config.svg" alt="config button" />
    </div>
  </div>
</template>

<script setup></script>

<style scoped>
.timer-box {
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
  cursor: pointer;
}

.timer-clock {
  grid-area: timer-clock;
}

.timer-buttons {
  grid-area: timer-buttons;
  display: flex;
  flex-direction: row;
  justify-content: space-evenly;
  height: 2.2rem;
  /* gap: 0.5rem; */
  /* cursor: pointer; */
}

.timer-buttons img {
  cursor: pointer;
}
</style>
