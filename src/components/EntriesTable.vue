<script setup>
const props = defineProps({
  eintraege: { type: Array, required: true },
})

const emit = defineEmits(['remove'])

function numberTableColor(typ) {
  return typ === 'Einnahme' ? 'positiveValue' : 'negativeValue'
}
</script>

<template>
  <section>
    <table>
      <thead>
        <tr>
          <th>Datum</th>
          <th>Beschreibung</th>
          <th>Kategorie</th>
          <th>Betrag</th>
          <th>Aktionen</th>
        </tr>
      </thead>

      <tbody>
        <tr v-for="(eintrag, index) in props.eintraege" :key="index">
          <td>{{ eintrag.datum }}</td>
          <td>{{ eintrag.beschreibung }}</td>
          <td>{{ eintrag.kategorie }}</td>
          <td :class="numberTableColor(eintrag.typ)">{{ eintrag.betrag }} €</td>
          <td>
            <button class="deleteButton" type="button" @click="emit('remove', index)">
              Entfernen
            </button>
          </td>
        </tr>
      </tbody>
    </table>
  </section>
</template>
