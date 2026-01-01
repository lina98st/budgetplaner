<script setup>
import './assets/base.css'
import './assets/main.css'

import { ref, computed, onMounted, watch } from 'vue'

import BudgetForm from './components/BudgetForm.vue'
import EntriesTable from './components/EntriesTable.vue'
import SummaryPanel from './components/SummaryPanel.vue'

const kategorien = [
  { name: 'Gehalt', typ: 'Einnahme' },
  { name: 'Geschenk', typ: 'Einnahme' },
  { name: 'Essen', typ: 'Ausgabe' },
  { name: 'Miete', typ: 'Ausgabe' },
  { name: 'Freizeit', typ: 'Ausgabe' },
  { name: 'Sonstiges', typ: 'Ausgabe' },
]

const eintraege = ref([])

function addEntry(entry) {
  eintraege.value.push(entry)
}

function removeEntry(index) {
  eintraege.value.splice(index, 1)
}

const totalExpenses = computed(() => {
  const v = eintraege.value
    .filter(e => e.typ === 'Ausgabe')
    .reduce((sum, e) => sum + e.betrag, 0)
  return Number(v.toFixed(2))
})

const totalIncome = computed(() => {
  const v = eintraege.value
    .filter(e => e.typ === 'Einnahme')
    .reduce((sum, e) => sum + e.betrag, 0)
  return Number(v.toFixed(2))
})

const accountBalance = computed(() => {
  const v = totalIncome.value - totalExpenses.value
  return Number(v.toFixed(2))
})

onMounted(() => {
  const saved = localStorage.getItem('budgetPlans')
  if (saved) eintraege.value = JSON.parse(saved)
})

watch(eintraege, (newVal) => {
  localStorage.setItem('budgetPlans', JSON.stringify(newVal))
}, { deep: true })
</script>

<template>
  <header>
    <h1>Budget Planer</h1>
  </header>

  <main id="budget-app">
    <BudgetForm :kategorien="kategorien" @add="addEntry" />

    <EntriesTable :eintraege="eintraege" @remove="removeEntry" />

    <SummaryPanel
      :total-income="totalIncome"
      :total-expenses="totalExpenses"
      :account-balance="accountBalance"
    />
  </main>
</template>
