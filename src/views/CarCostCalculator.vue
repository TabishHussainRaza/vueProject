<script setup>
import { ref, computed } from 'vue'

let selectedVehicleType = ref('')
let selectedMake = ref('')
let selectedModel = ref('')
let selectedYear = ref('')
let selectedVariant = ref('')

let kmDriven = ref(1200)
let averageFuelPrice = ref(1.83)
let runningCost = ref(0)
let insurance = ref(0)

class Variant {
  constructor(name, price) {
    this.name = name
    this.price = price
  }
}

class VehicleModel {
  constructor(name, year, type, variants) {
    this.name = name
    this.year = year
    this.type = type
    this.variants = variants.map((variant) => new Variant(variant.name, variant.price))
  }
}

class CarBrand {
  constructor(name, models) {
    this.name = name
    this.models = models.map(
      (model) => new VehicleModel(model.name, model.year, model.type, model.variants)
    )
  }
}

const vehicles = ref([
  new CarBrand('Toyota', [
    {
      name: 'Camry',
      year: 2022,
      type: 'petrol',
      variants: [
        { name: 'LE', price: 10000 },
        { name: 'SE', price: 10000 }
      ]
    },
    {
      name: 'Corolla',
      year: 2023,
      type: 'petrol',
      variants: [
        { name: 'L', price: 10000 },
        { name: 'XLE', price: 10000 }
      ]
    }
  ]),
  new CarBrand('Honda', [
    {
      name: 'Civic',
      year: 2022,
      type: 'petrol',
      variants: [
        { name: 'DX', price: 10000 },
        { name: 'EX', price: 10000 }
      ]
    },
    {
      name: 'Accord',
      year: 2023,
      type: 'petrol',
      variants: [
        { name: 'LX', price: 10000 },
        { name: 'Sport', price: 10000 }
      ]
    }
  ])
])

let selectedBrand = computed(() => {
  return vehicles.value.find((vehicle) => vehicle.name === selectedMake.value) || null
})

let years = computed(() => {
  return selectedBrand.value ? [selectedBrand.value.models[0].year] : []
})

let variants = computed(() => {
  return selectedBrand.value ? selectedBrand.value.models[0].variants : []
})

let selectedUserVariant = computed(() => {
  return selectedBrand.value && selectedVariant.value
    ? selectedBrand.value.models[0].variants.find(
        (variant) => variant.name === selectedVariant.value
      )
    : null
})

let showFirstForm = ref(true)
let showSecondForm = ref(false)

const toggleForm = () => {
  showFirstForm.value = !showFirstForm.value
  showSecondForm.value = !showFirstForm.value
}

let totalCost = ref(null)

const calculateCosts = () => {
  console.log(selectedUserVariant)
  const variantPrice = selectedUserVariant?.price || 0
  const fuelCost = kmDriven.value * averageFuelPrice.value
  const totalRunningCost = runningCost.value || 0
  const insuranceCost = insurance.value || 0

  totalCost.value = variantPrice + fuelCost + totalRunningCost + insuranceCost
  showSecondForm.value = false
}
</script>

<template>
  <main>
    <form v-if="showFirstForm">
      <label for="vehicleType">Vehicle Type:</label>
      <select v-model="selectedVehicleType">
        <option value="petrol">Petrol</option>
        <option value="electric">Electric</option>
      </select>

      <label for="make">Brand/Make:</label>
      <select v-model="selectedMake" @change="updateModels">
        <option v-for="vehicle in vehicles" :value="vehicle.name" :key="vehicle.name">
          {{ vehicle.name }}
        </option>
      </select>

      <label for="model">Car Model:</label>
      <select v-model="selectedModel" :disabled="!selectedMake">
        <option v-for="model in selectedBrand?.models" :value="model.name" :key="model.name">
          {{ model.name }}
        </option>
      </select>

      <label for="year">Year:</label>
      <select v-model="selectedYear" :disabled="!selectedModel">
        <option v-for="year in years" :value="year" :key="year">{{ year }}</option>
      </select>

      <label for="variant">Variant:</label>
      <select v-model="selectedVariant" :disabled="!selectedYear">
        <option v-for="variant in variants" :value="variant.name" :key="variant.name">
          {{ variant.name }}
        </option>
      </select>
      <button type="button" @click="toggleForm">Next</button>
    </form>
    <form v-if="showSecondForm" @submit.prevent="submitSecondForm">
      <label for="kmDriven">Kilometers Driven:</label>
      <input v-model="kmDriven" type="number" required />

      <label for="averageFuelPrice">Average Fuel Price:</label>
      <input v-model="averageFuelPrice" type="number" step="0.01" required />

      <label for="runningCost">Running Cost:</label>
      <input v-model="runningCost" type="number" step="0.01" required />

      <label for="insurance">Insurance:</label>
      <input v-model="insurance" type="number" step="0.01" required />

      <button type="button" @click="calculateCosts">Calculate</button>
    </form>

    <div v-if="totalCost !== null">
      <h2>Total Cost</h2>
      <table>
        <tr>
          <td>Vehicle Price</td>
          <td>${{ selectedUserVariant?.price.toFixed(2) || 0 }}</td>
        </tr>
        <tr>
          <td>Fuel Cost</td>
          <td>${{ (kmDriven * averageFuelPrice).toFixed(2) }}</td>
        </tr>
        <tr>
          <td>Running Cost</td>
          <td>${{ runningCost.toFixed(2) }}</td>
        </tr>
        <tr>
          <td>Insurance Cost</td>
          <td>${{ insurance.toFixed(2) }}</td>
        </tr>
        <tr>
          <td><strong>Total</strong></td>
          <td>
            <strong>${{ totalCost.toFixed(2) }}</strong>
          </td>
        </tr>
      </table>
    </div>
  </main>
</template>

<style>
body {
  font-family: Arial, sans-serif;
  display: flex;
  align-items: center;
  justify-content: center;
  height: 100vh;
  margin: 0;
}

form {
  max-width: 400px;
  margin: auto;
  padding: 20px;
  border: 1px solid #ccc;
  border-radius: 5px;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
}

label {
  display: block;
  margin-bottom: 8px;
}

select,
input {
  width: 100%;
  padding: 10px;
  margin-bottom: 20px;
  box-sizing: border-box;
}

button {
  background-color: #4caf50;
  color: white;
  padding: 10px 15px;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}

button:hover {
  background-color: #45a049;
}
table {
  width: 100%;
  border-collapse: collapse;
  margin-top: 20px;
}

th, td {
  border: 1px solid #ddd;
  padding: 12px;
  text-align: left;
}

th {
  background-color: #4caf50;
  color: white;
}

td {
  background-color: #f9f9f9;
}
</style>
