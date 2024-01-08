<template>
  <div id="app" class="container mt-5">
    <div class="card p-4 shadow-sm rounded">
      <h1 class="mb-4">Notes App</h1>

      <form @submit.prevent="storeNote">

        <div class="mb-3">
          <label for="noteText" class="form-label">Text of the note:</label>
          <input type="text" id="noteText" v-model="newNoteText" class="form-control" placeholder="Enter text for the note" required>
        </div>

        <div class="mb-3">
          <label class="form-label">Type of the note:</label>
          <div class="btn-group" role="group">
            <input type="radio" id="remind" value="remind" v-model="newNoteType" class="btn-check">
            <label for="remind" class="btn btn-outline-primary">Remind</label>

            <input type="radio" id="task" value="task" v-model="newNoteType" class="btn-check">
            <label for="task" class="btn btn-outline-primary">Task</label>
          </div>
        </div>

        <div class="mb-3">
          <label class="form-label">Importance:</label>
          <select v-model="newNotePriority" class="form-select">
            <option value="low">Low</option>
            <option value="high">High</option>
          </select>
        </div>

        <button type="submit" class="btn btn-primary">Add note</button>
        <button type="button" @click="deleteAllNotes" class="btn btn-danger ms-2">Delete all notes</button>

      </form>

      <ul class="list-group mt-3">
        <li v-for="(note, index) in sortedNotes" :key="index" class="list-group-item">
          <div class="d-flex justify-content-between align-items-center">
            <div :class="{ 'text-decoration-line-through': note.completed, 'font-weight-bold': note.priority === 'high'}">
              <strong>{{ note.type }}</strong> - {{ note.text }}
            </div>
            <div>
              <input type="checkbox" v-model="note.completed" class="form-check-input" :id="'completedItem' + index">
              <label :for="'completedItem' + index" class="form-check-label ms-2">Completed</label>
              <button @click="deleteNoteById(index)" class="btn btn-outline-danger ms-2">Delete</button>
            </div>
          </div>
        </li>
      </ul>

    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      newNoteText: '',
      newNoteType: 'personal',
      newNotePriority: 'hight',
      newNoteCompleted: false,
      notes: JSON.parse(localStorage.getItem('notes')) || [],
    };
  },
  computed: {
    sortedNotes() {
      return this.notes.slice().sort((a, b) => {
        const priorityOrder = {
          'low': 1,
          'hight': 2
        };
        const priorityComparison = priorityOrder[b.priority] - priorityOrder[a.priority];
        return priorityComparison !== 0 ? priorityComparison : (b.completed - a.completed);
      });
    },
  },
  methods: {
    storeNote() {
      const newNote = {
        text: this.newNoteText,
        type: this.newNoteType,
        priority: this.newNotePriority,
        completed: this.newNoteCompleted,
      };
      if (this.newNoteText.length === 0) {
        return;
      }

      this.notes.push(newNote);
      this.storeData();
      this.cleanFormForNote();
    },
    storeData() {
      localStorage.setItem('notes', JSON.stringify(this.notes));
    },
    cleanFormForNote() {
      this.newNoteText = '';
      this.newNoteType = 'personal';
      this.newNotePriority = 'low';
      this.newNoteCompleted = false;
    },
    deleteAllNotes() {
      localStorage.removeItem('notes');
      this.notes = [];
    },
    deleteNoteById(index) {
      this.notes.splice(index, 1);
      this.storeData();
    },
  },
};
</script>

<style>
#app {
  font-family: 'Avenir', 'Helvetica', 'Arial', sans-serif;
  text-align: center;
  color: #333;
  margin-top: 60px;
}

.card {
  box-shadow: 0 0 15px rgba(0, 0, 0, 0.1);
  border-radius: 10px;
  padding: 20px;
}

.form-group {
  margin-bottom: 1.5rem;
}

.form-check {
  display: flex;
  align-items: center;
  margin-right: 1rem;
}

.btn {
  cursor: pointer;
  transition: background-color 0.3s ease, color 0.3s ease, border-color 0.3s ease;
}

.btn-primary {
  background-color: #3498db;
  color: #fff;
  border: 1px solid #3498db;
}

.btn-danger {
  background-color: #e74c3c;
  color: #fff;
  border: 1px solid #e74c3c;
}

.list-group-item {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-top: 0.75rem;
  padding: 1rem;
  border: 1px solid #d4d4d4;
  border-radius: 5px;
  transition: background-color 0.3s ease;
}

.list-group-item:hover {
  background-color: #f4f4f4;
}

.text-decoration-line-through {
  text-decoration: line-through;
}

.hight {
  font-weight: 900;
}

</style>
