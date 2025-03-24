<script setup>
import TitleInput from './components/TitleInput.vue'
import {ref} from 'vue'

let tideData = ref([])
let tideDataLength = tideData.value.length

const handleData = (data) => {
  tideData.value = data
  tideDataLength = tideData.value.length
  //console.log(tideData.value)
}

</script>

<template>
  <header>
    <img class="logo" src="./assets/fishforecasticon.png" width="200" height="200" />
    <div class="wrapper">
      <TitleInput msg="Fish-Forecast" @send-data="handleData"/>
    </div>
  </header>

  <main>
    <ul v-for="tide in tideData" :key="tide.id">
      <img class="logo" src="./assets/fish.png" width="40" height="40" />
      <h2>Time: {{ tide.t }}</h2>
      <h2>Wave Height(meters): {{ tide.v }}</h2>
      <h2 v-if="tide.type === 'H'"><span class="tideText">High Tide:</span> Try fishing for surf perch, leopard shark, bat rays, or striped bass!</h2>
      <h2 v-if="tide.type === 'L'"><span class="tideText">Low Tide:</span> Try fishing for surf perch, croaker, or halibut/flounder!</h2>
    </ul>
    <ul v-if="tideDataLength === 0">
      <img class="logo" src="./assets/fish.png" width="100" height="100" />
      <h1>
        Find out your predicted surf fishing success based on location!
      </h1>
      <div class="catext">
        <li>
          <h2>Supports only the state of California for now</h2>
        </li>
      </div>
    </ul>
  </main>


</template>

<style scoped>
header {
  line-height: 1.5;
}

.tideDisplay {
  
}

ul {
  margin-top: 40px;
}

.catext {
  padding-left: 40px;
}

.tideText {
  font-weight: bold;
}

.logo {
  display: block;
  margin: 0 auto 2rem;
}

@media (min-width: 1024px) {
  header {
    display: flex;
    place-items: center;
    padding-right: calc(var(--section-gap) / 2);
  }

  .logo {
    margin: 0 2rem 0 0;
  }

  header .wrapper {
    display: flex;
    place-items: flex-start;
    flex-wrap: wrap;
  }
}
</style>
