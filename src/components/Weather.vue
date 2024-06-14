<template>
  <div class="weather-container q-pa-md">
    <div class="form-container">
      <h4 class="q-mb-md form-title">City Weather</h4>
      <form @submit.prevent="getCuaca" class="q-gutter-md form-inline">
        <q-input
          outlined
          square
          v-model="kota"
          label="Masukkan nama kota"
          autofocus
          class="form-input"
        />
        <q-btn
          square 
          type="submit"
          color="dark"
          icon="navigation"
          class="form-button"
          size="21px"
        />
      </form>
    </div>

    
    <q-spinner-puff v-if="sedangMemuat" color="dark" size="5em" class="q-mt-lg" />


    <p v-if="!sedangMemuat && !dataCuacaAda" class="q-mt-lg">Masukkan nama kota untuk mendapatkan informasi cuaca.</p>

    
    <div v-if="dataCuacaAda && !sedangMemuat" class="q-mt-lg weather-info">
      <p>Cuaca Saat Ini: {{ cuacaSaatIni }}</p>
      <p>Suhu: {{ suhu }}°C</p>
      <p>Kelembaban: {{ kelembaban }}%</p>
      <p>Kecepatan Angin: {{ kecepatanAngin }} m/s</p>
    </div>
  </div>
</template>

<script>
import { ref } from 'vue';
import axios from 'axios';

export default {
  name: 'Weather',
  setup() {
    const kota = ref(''); 
    const sedangMemuat = ref(false); 
    const dataCuacaAda = ref(false);
    const cuacaSaatIni = ref(''); 
    const suhu = ref(0); 
    const kelembaban = ref(0); 
    const kecepatanAngin = ref(0); 

    
    const getCuaca = async () => {
      sedangMemuat.value = true; 
      dataCuacaAda.value = false; 
      const apiKey = '3e7f83bf90793f6d8111d59a663972f4'; 
      
      const apiUrl = `https://api.openweathermap.org/data/2.5/weather?q=${kota.value}&appid=${apiKey}&units=metric`;
      
      try {
        const response = await axios.get(apiUrl);
        const data = response.data;
        cuacaSaatIni.value = data.weather[0].description;
        suhu.value = data.main.temp;
        kelembaban.value = data.main.humidity;
        kecepatanAngin.value = data.wind.speed;
        dataCuacaAda.value = true; 
        
        
        setTimeout(() => {
          sedangMemuat.value = false; 
        }, 2000);
        
      } catch (error) {
        console.error('Error fetching weather data:', error);
        sedangMemuat.value = false; 
      }
    };

    return {
      kota,
      sedangMemuat,
      dataCuacaAda,
      cuacaSaatIni,
      suhu,
      kelembaban,
      kecepatanAngin,
      getCuaca
    };
  }
}
</script>

<style scoped>
.weather-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  margin-top: 20px;
}

.form-container {
  width: 100%;
  max-width: 400px;
}

.form-title {
  text-align: center;
}

.form-inline {
  display: flex;
  align-items: center;
}

.form-input {
  flex: 1;
}

.form-button {
  margin-left: 8px;
}

.weather-info {
  background: #ffffff;
  padding: 20px;
  border-radius: 10px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
  max-width: 400px;
  width: 100%;
  text-align: center;
}
</style>
