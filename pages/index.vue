<template>
  <div>
    <!-- tailwind styled card  -->
    <div class="m-3 border">
      <textarea v-model="noteText" class="p-3 w-full h-full outline-none" @blur="saveANote()" />
    </div>

    <div v-for="note in notes" :key="note.id" class="m-3 border">
      <span class=" whitespace-pre">
        {{ note.text }}
      </span>
    </div>
  </div>
</template>

<script lang="ts">
import { Vue, Component } from 'vue-property-decorator'

interface Note {
  id:string
  text:string
}

@Component
export default class MainApp extends Vue {
  noteText: string = ''
  notes: Note[] = JSON.parse(localStorage.notes || '[]')

  saveANote () {
    if (this.noteText === '') {
      return
    }
    this.notes.push({
      id: 'id_' + Math.random(),
      text: this.noteText.trim()
    })
    this.noteText = ''
    localStorage.notes = JSON.stringify(this.notes)
  }
}
</script>
