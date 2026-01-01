<script setup>
const props = defineProps({
  totalIncome: { type: Number, required: true },
  totalExpenses: { type: Number, required: true },
  accountBalance: { type: Number, required: true },
})

function balanceColorClass() {
  if (props.accountBalance < 0) return 'negative'
  if (props.accountBalance > 0) return 'positive'
  return ''
}
</script>

<template>
  <aside id="summen">
    <p>Gesamteinnahmen: {{ props.totalIncome }} €</p>
    <p>Gesamtausgaben: {{ props.totalExpenses }} €</p>
    <p :class="balanceColorClass()">Kontostand: {{ props.accountBalance }} €</p>
  </aside>
</template>
