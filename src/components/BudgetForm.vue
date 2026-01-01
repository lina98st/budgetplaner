<script setup>
const props = defineProps({
  kategorien: { type: Array, required: true },
})

const emit = defineEmits(['add'])

import { ref, computed } from 'vue'

const beschreibung = ref('')
const betrag = ref(null)
const kategorie = ref('')
const typ = ref('Einnahme')

const isValid = computed(() => {
  return kategorie.value && typ.value && Number(betrag.value) > 0
})

function onSubmit() {
  if (!isValid.value) {
    alert('Please fill all fields and enter a positive amount.')
    return
  }

  const gerundeterBetrag = Number(Number(betrag.value).toFixed(2))

  emit('add', {
    datum: new Date().toLocaleDateString(),
    beschreibung: beschreibung.value,
    kategorie: kategorie.value,
    betrag: gerundeterBetrag,
    typ: typ.value,
  })

  beschreibung.value = ''
  betrag.value = null
  kategorie.value = ''
  typ.value = 'Einnahme'
}
</script>

<template>
  <form @submit.prevent="onSubmit">
    <select v-model="kategorie" id="categories" name="categories">
      <option value="">Kategorie wählen</option>
      <option v-for="kat in props.kategorien" :key="kat.name" :value="kat.name">
        {{ kat.name }}
      </option>
    </select>

    <select v-model="typ" id="typ" name="typ">
      <option value="Einnahme">Einnahme</option>
      <option value="Ausgabe">Ausgabe</option>
    </select>

    <input
      class="betrag"
      type="number"
      id="betrag"
      name="betrag"
      placeholder="Betrag"
      step="0.01"
      v-model.number="betrag"
    >

    <input
      type="text"
      class="text-field"
      placeholder="Beschreibung"
      id="beschreibung"
      name="beschreibung"
      v-model="beschreibung"
    >

    <button class="hinzu" type="submit">Hinzufügen</button>
  </form>
</template>
