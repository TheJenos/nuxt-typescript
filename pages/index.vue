<template>
  <div class="flex justify-center">
    <div class="md:my-6 w-full md:w-1/2">
      <h1 class="text-center">
        Note
      </h1>
      <div class="m-3 border rounded-xl p-3">
        <textarea v-model="noteText" class="w-full h-full text-lg outline-none" @blur="saveANote()" />
      </div>

      <div v-for="note in notes" :key="note.id">
        <transition>
          <div class="m-3 border p-3 rounded-xl animate" :class="note.color">
            <div class="flex float-right">
              <span class="ml-auto text-sm text-gray-50">{{ convertDateToString(note.lastEdit) }}</span>
              <i class="ml-1 my-auto text-sm text-white fas fa-trash" @click="deleteANote(note)" />
            </div>
            <span v-if="!selectedNote || note.id != selectedNote.id" class="mt-2 block w-full h-full text-lg text-white whitespace-pre-line" @click="markAsEdit(note)" v-text="note.text" />
            <textarea
              v-else
              ref="updateText"
              v-model="note.text"
              :rows="getLineCount(note.text)"
              class="w-full h-full outline-none rounded-xl mt-2 p-2"
              @blur="saveANote()"
            />
          </div>
        </transition>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { Vue, Component } from 'vue-property-decorator'

const colors = [
  'bg-blue-200',
  'bg-blue-400',
  'bg-indigo-300',
  'bg-green-400',
  'bg-teal-400',
  'bg-yellow-200'
]

interface Note {
  id:string
  text:string
  lastEdit:Date,
  color:string
}

@Component
export default class MainApp extends Vue {
  noteText: string = ''
  selectedNote: Note | null = null
  notes: Note[] = JSON.parse(localStorage.notes || '[]')

  $refs!: {
    updateText: HTMLFormElement[]
  }

  saveANote () {
    if (this.selectedNote) {
      this.selectedNote.lastEdit = new Date()
      if (this.selectedNote.text === '') {
        this.deleteANote(this.selectedNote)
      }
      this.selectedNote = null
    } else {
      if (this.noteText === '') {
        return
      }
      this.notes.unshift({
        id: 'id_' + Math.random(),
        text: this.noteText.trim(),
        lastEdit: new Date(),
        color: colors[this.notes.length % colors.length]
      })
    }
    this.noteText = ''
    localStorage.notes = JSON.stringify(this.notes)
  }

  markAsEdit (note: Note) {
    this.selectedNote = note
    this.$nextTick(function () {
      this.$refs.updateText[0].focus()
    })
  }

  deleteANote (note: Note) {
    this.notes = this.notes.filter(n => n.id !== note.id)
    localStorage.notes = JSON.stringify(this.notes)
  }

  convertDateToString (date: Date) {
    date = new Date(date)
    return `${date.getDate()}/${date.getMonth() + 1}/${date.getFullYear()} ${date.getHours()}:${date.getMinutes()}`
  }

  getLineCount (note: String) {
    return note.split('\n').length
  }
}
</script>

<style scoped>
.animate{
   transition: all 2s ease-in-out;
}

</style>
