<script setup>
import {ref, defineEmits, onMounted} from 'vue'

defineProps({
  msg: {
    type: String,
    required: true,
  },
})

let caStations = ref([])
let selectedValue = ref("")
let tideData = ref([])
let errorMsg = ref(false)

let emit = defineEmits()

const fetchCAStations = async() => {
  await fetch('https://api.tidesandcurrents.noaa.gov/mdapi/prod/webapi/stations.json?type=benchmarks', {
    method: 'GET',
    headers: {
      'Accept': 'application/json',  // Ensure server returns JSON
      'Content-Type': 'application/json'
    }
  })
  .then(res => res.json())
  .then(data => {
    let dataArray = data.stations
    let californiaStation = dataArray.filter(item => item.state === "CA")
    caStations.value = californiaStation
  })
  .catch(err => console.log(err.message))
  //console.log(caStations.value)
}

const fetchStationData = async() => {
  await fetch('https://api.tidesandcurrents.noaa.gov/api/prod/datagetter?station=' + selectedValue.value + '&product=predictions&datum=MLLW&units=metric&time_zone=lst_ldt&format=json&interval=hilo&range=24', {
    method: 'GET',
    headers: {
      'Accept': 'application/json',  // Ensure server returns JSON
      'Content-Type': 'application/json'
    }
  })
  .then(res => res.json())
  .then(data => {
    tideData.value = []
    if(data.predictions) {
      errorMsg.value = false
      let predictions = data
      tideData.value = predictions.predictions
    }
    else {
      errorMsg.value = true
    }
  })
  .catch(err => console.log(err.message))
  //console.log(tideData.value)
  sendDataToParent(tideData.value)
}

const sendDataToParent = (data) => {
  emit('send-data', data)
}

onMounted(() => {
  fetchCAStations()
})

</script>

<template>
  <div class="greetings">
    <h1 class="teal">{{ msg }}</h1>

    <label for="cars">Choose a city:</label>
    <br>
    <div class = "dropdown">
      <select v-model="selectedValue">
        <option v-for="station in caStations" :key="station.id" :value="station.id">{{ station.name }}</option>
      </select>
      <button @click="fetchStationData()">Confirm</button>
    </div>
    <p v-if="errorMsg" class="errorMsg">This city does not have tide information. Please select another city.</p>
  </div>
</template>

<style scoped>
h1 {
  font-weight: 500;
  font-size: 2.6rem;
  position: relative;
  top: -10px;
}

h3 {
  font-size: 1.2rem;
}

.greetings h1,
.greetings h3 {
  text-align: center;
}

@media (min-width: 1024px) {
  .greetings h1,
  .greetings h3 {
    text-align: left;
  }
}

.dropdown {
  display: flex;
  gap: 10px;
}

select {
  flex-grow: 1;
  padding: 10px;
}

.errorMsg {
  color: red;
}

</style>
