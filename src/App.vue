<script setup>
import './assets/base.css'; // Importiere die globale CSS-Datei
import './assets/main.css'; // Spezifische Stile für Layouts oder Komponenten

import { ref, computed, onMounted, watch } from 'vue'


  const kategorien = [
  { name: 'Gehalt', typ: 'Einnahme' },
  { name: 'Geschenk', typ: 'Einnahme' },
  { name: 'Essen', typ: 'Ausgabe' },
  { name: 'Miete', typ: 'Ausgabe' },
  { name: 'Freizeit', typ: 'Ausgabe' },
  { name: 'Sonstiges', typ: 'Ausgabe' },
];

const eintraege = ref([]);

//Eingabewerte binden
const beschreibung = ref('');
const betrag = ref(0);
const kategorie = ref('');
const typ = ref(''); // Income or expense?



//Button Klick
function buttonHinzu() {
const gerundeterBetrag = parseFloat(parseFloat(betrag.value).toFixed(2)); // neu
    const neuerEintrag = { 
      datum: new Date().toLocaleDateString(),
      beschreibung: beschreibung.value,
      kategorie: kategorie.value,
      betrag: gerundeterBetrag, 
      typ: typ.value, // Save type (income or expense)
    };

//Notification for missing fields
    if (!kategorie.value || !typ.value || betrag.value <= 0) {
    alert("Bitte alle Felder ausfüllen und einen positiven Betrag eingeben.");
    return;
  }

    //Add new item to the list
    eintraege.value.push(neuerEintrag);
    //Reset 
    beschreibung.value = '';
    betrag.value = 0;
    kategorie.value = '';
    typ.value = '';
  }

  //Functions to remove an item
  function eintragEntfernen(index) {
    eintraege.value.splice(index,1)

      // Aktualisiere den `localStorage` manuell
  localStorage.setItem('budgetPlans', JSON.stringify(eintraege.value));
  }

  //calculate account balance
  const accountBalance = computed(() => {
    const balanca = totalIncome.value - totalExpenses.value;
return parseFloat(balanca.toFixed(2));

  });

  //calculate total Expense
  const totalExpenses = computed(() => {
    const expenses = eintraege.value
    .filter(eintrag => eintrag.typ === 'Ausgabe')
    .reduce((sum, eintrag) => sum + eintrag.betrag, 0);

return parseFloat(expenses.toFixed(2)); 
  });

//Calculate income
  const totalIncome = computed(() => {
    const income = eintraege.value
    .filter(eintrag => eintrag.typ === 'Einnahme')
    .reduce((sum, eintrag) => sum + eintrag.betrag, 0);

return parseFloat(income.toFixed(2));
  });

//localStorage Data and Key
onMounted(() => {
const savedBudgetData = localStorage.getItem('budgetPlans');
if (savedBudgetData) {
  eintraege.value = JSON.parse(savedBudgetData);

}
});

//watch changes for localStorage
watch(eintraege, (newBudgetData) => {
localStorage.setItem('budgetPlans', JSON.stringify(newBudgetData));
}, { deep: true });

//Computed Property for CSS-class
const balanceColorClass = computed(() => {
if (accountBalance.value < 0) {
return 'negative'; 
 } else if (accountBalance.value > 0) {
  return 'positive';
 } else { 
  return '';
 }
});

function numberTableColor(typ) {
  return typ === 'Einnahme' ? 'positiveValue' : 'negativeValue';
}

</script>

<template>
  <header>
  <h1>Budget Planer</h1>
  </header>
  <main id="budget-app">
    <form @submit.prevent="buttonHinzu"> <!-- Verhindert das Neuladen der Seite -->
      <select v-model="kategorie" id="categories" name="categories"> 
        <option value="">Kategorie wählen</option>
<option v-for="kat in kategorien" :key="kat.name" :value="kat.name">
  {{ kat.name }}
  </option>
      </select>
      <select v-model="typ" id="typ" name="typ"> 
        <option value="Einnahme">Einnahme</option>
        <option value="Ausgabe">Ausgabe</option>
        </select>
        <input class="betrag" type="number" id="betrag" name="betrag" placeholder="Betrag" step="0.01" v-model.number="betrag"> 
  <input type="text" class="text-field" placeholder="Beschreibung" id="beschreibung" name="beschreibung" v-model="beschreibung">
      <button class="hinzu" type="submit">Hinzufügen</button> 
</form>

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
          <tr v-for="(eintrag, index) in eintraege" :key="index">
            <td>{{ eintrag.datum }}</td>
            <td>{{ eintrag.beschreibung }}</td>
            <td>{{ eintrag.kategorie }}</td>
            <td :class="numberTableColor(eintrag.typ)">{{ eintrag.betrag }} €</td>
            <td>
              <button class="deleteButton" @click="eintragEntfernen(index)">Entfernen</button>
            </td>
          </tr>
        </tbody>
      </table>
    </section>
      <aside id="summen">
        <p>Gesamteinnahmen: {{ totalIncome }} €</p>
        <p>Gesamtausgaben: {{ totalExpenses }} €</p>
        <p :class="balanceColorClass">Kontostand: {{ accountBalance }} €</p>
      </aside>
  </main>
</template>

<style scoped>


</style>
