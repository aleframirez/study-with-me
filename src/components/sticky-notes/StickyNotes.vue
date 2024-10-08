<template>
  <section
    class="new-note-button"
    :style="{ left: `${posX}px`, top: `${posY}px` }"
  >
    <button @click="createNote">New Note</button>
    <NotesComponent
      v-for="(note, index) in notes"
      :key="note.id"
      :note="note"
      @update-note="updateNote"
      @delete-note="deleteNote(index)"
    />
  </section>
</template>

<script setup>
import { onMounted, onUnmounted, ref } from "vue";
import NotesComponent from "./NotesComponent.vue";

const posX = ref(65); // Posición inicial en X
const posY = ref(325); // Posición inicial en Y

const notes = ref([]);

// Crear una nueva nota vacia
const createNote = () => {
  const variableY = getRandomNumber(100, -250);
  const variableX = getRandomNumber(100, 1000);

  function getRandomNumber(x, y) {
    return Math.floor(Math.random() * (x - y + 1)) + y;
  }
  const newNote = {
    positionY: variableY,
    positionX: variableX,
    title: "Title",
    content: "Note content",
    id: new Date().getTime(),
  };
  notes.value.push(newNote);
  saveNotes();
};

// Guardar los datos en el localStorage
const saveNotes = () => {
  localStorage.setItem("notes", JSON.stringify(notes.value));
};

// Cargar las notas del localStorage al inicio
onMounted(() => {
  const storedNotes = JSON.parse(localStorage.getItem("notes"));
  if (storedNotes) {
    notes.value = storedNotes;
  }
});

// Actualizar una nota
const updateNote = (updateNote) => {
  const noteIndex = notes.value.findIndex((note) => note.id === updateNote.id);
  if (noteIndex !== -1) {
    notes.value[noteIndex] = updateNote;
    saveNotes();
  }
};

// Eliminar una nota
const deleteNote = (index) => {
  notes.value.splice(index, 1);
  saveNotes();
};
</script>

<style scoped>
.new-note-button {
  position: absolute;
  user-select: none;
}

.new-note-button button {
  display: block;
  padding: 0.5rem;
  border-radius: 0.25rem;
  color: var(--text-light-mode);
  font-weight: 600;
  cursor: pointer;
  transition: 0.2s ease-in-out;
  background-color: var(--lateral-dark-mode);
  border: 2px solid var(--text-light-mode);
}
</style>
