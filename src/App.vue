<script setup>
import { ref, onMounted } from "vue";

const showModal = ref(false);
const newNote = ref("")
const errorMsg = ref("")
const notes = ref([]);

onMounted(() => {
  const storedNotes = localStorage.getItem('notes')
  if (storedNotes) {
    notes.value = JSON.parse(storedNotes)
  }
})


function getRandomColor() {
  const color = "hsl(" + Math.random() * 360 + ", 100%, 75%)";
  return color;
}

const addNote = () => {
  if (newNote.value.length > 9) {
    notes.value.push({
      id: Date.now().toString(32) + Math.random().toString(32).substring(2),
      text: newNote.value,
      date: new Date().toLocaleDateString("en-US"),
      bgColor: getRandomColor()
    })

    localStorage.setItem('notes', JSON.stringify(notes.value))

    showModal.value = false;
    newNote.value = "";
    errorMsg.value = ""
  } else {
    errorMsg.value = "*Add more than 10 characters!"
  }
}

const deleteNote = (id) => {
  notes.value = notes.value.filter(note => note.id !== id);
  localStorage.setItem('notes', JSON.stringify(notes.value))
}

</script>

<template>
  <main>
    <div v-if="showModal" class="overlay">
      <div class="modal">
        <p v-if="errorMsg">{{ errorMsg }}</p>
        <textarea v-model.trim="newNote" @change="onChange" name="notes" id="notes" cols="30" rows="10"
          maxlength="120"></textarea>
        <button @click="addNote">Add Note</button>
        <button class="close" @click="showModal = false">x</button>
      </div>
    </div>

    <div class="container">
      <header>
        <h1>Notes</h1>
        <button @click="showModal = true">+</button>
      </header>

      <div class="cards-container" v-if="notes.length">
        <div class="card" v-for="note in  notes " :key="note.id" :style="{ backgroundColor: note.bgColor }">
          <button @click="deleteNote(note.id)">x</button>
          <p class="main-text">{{ note.text }}</p>
          <p class="date">{{ note.date }}</p>
        </div>
      </div>
    </div>
  </main>
</template>

<style scoped>
/* GENERAL */
main {
  height: 100vh;
  width: 100vw;
}

.container {
  max-width: 100rem;
  padding: 1rem;
  margin: 0 auto;
}

header {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

h1 {
  font-weight: bold;
  margin-bottom: 2.5rem;
  font-size: 7.5rem;
  user-select: none;
}

header button {
  border: none;
  padding: 1rem;
  width: 5rem;
  height: 5rem;
  cursor: pointer;
  background-color: rgb(21, 20, 20);
  color: var(--vt-c-white);
  border-radius: 100%;
  font-size: 20px;
}

@media (prefers-color-scheme: dark) {
  button {
    background-color: var(--vt-c-white);
    color: var(--vt-c-black);
  }

  /* CARD */
  .cards-container {
    display: flex;
    flex-wrap: wrap;
    gap: 2rem;
  }

  .card {
    min-width: 25rem;
    height: 25rem;
    background-color: rgb(237, 182, 44);
    padding: 1rem;
    border-radius: 1.5rem;
    display: flex;
    flex-direction: column;
    justify-content: space-between;
    margin: 0 2rem 2rem 0;
    position: relative;
  }


  .card button {
    position: absolute;
    right: 10px;
    cursor: pointer;
  }

  .main-text {
    margin-top: 2rem;
  }

  .main-text,
  .date {
    color: var(--vt-c-black);
  }

  .date {
    font-size: 1.2rem;
    font-weight: bold;
  }

  /* MODAL */
  .overlay {
    position: absolute;
    width: 100%;
    height: 100%;
    background-color: rgba(0, 0, 0, 0.77);
    z-index: 10;
    display: flex;
    align-items: center;
    justify-content: center;
  }

  .modal {
    max-width: 75rem;
    background-color: var(--vt-c-white);
    border-radius: 1rem;
    padding: 3rem;
    position: relative;
    display: flex;
    flex-direction: column;
  }

  .modal p {
    color: var(--vt-c-black);
    font-weight: bold;
    font-size: 1rem;
    margin-bottom: .5rem;
    user-select: none;
  }

  .modal textarea {
    resize: none;
    padding: .1rem .5rem;
    line-height: 1.5;
    letter-spacing: 1px;
  }

  .modal button {
    padding: 1rem 2rem;
    font-size: 1.8rem;
    width: 100%;
    background-color: var(--vt-c-text-yellow);
    border: none;
    color: var(--vt-c-white);
    cursor: pointer;
    margin-top: 1.5rem;
    color: var(--vt-c-black);
  }

  .modal .close {
    position: absolute;
    top: 1px;
    right: 1.5rem;
    width: 1rem;
    margin: 0;
    padding: 1rem;
    background-color: unset;

  }
}
</style>