<template>
  <v-container class="d-flex flex-column justify-center align-center" height="100vh">
    <v-btn @click="toggleTheme" variant="outlined" class="text-capitalize">toggle theme</v-btn>
    <v-card
      class="mt-5 pa-10 elevation-4 rounded-lg"
      width="450"
      
    >
      <v-row class="d-flex flex-column">
        <v-col class="pa-0">
          <h1 class="text-center">Weather App</h1>
        </v-col>
        <v-col class="pa-0 mt-5">
          <v-form @submit.prevent="handleSubmit">
            <v-text-field
              ref="inputRef"
              label="Search"
              append-inner-icon="mdi-magnify"
              variant="outlined"
              clearable 
            ></v-text-field>
          </v-form>
        </v-col>

        <v-col class="pa-0">
          <div class="d-flex flex-column justify-center align-center">
            <img :src="icons[weatherData.icon]" alt="weather-icon" v-if="weatherData.icon" />
            <p class="temperature">{{ weatherData.temperature }}°C</p>
            <p class="location text-h5">{{ weatherData.location }}</p>
          </div>
        </v-col>
        
        <v-col class="pa-0 mt-15">
          <div class="d-flex">
            <div class="d-flex justify-start align-center flex-grow-1">
              <v-icon icon="mdi-waves" size="x-large" class="mr-5"></v-icon>
              <div>
                <p>{{ weatherData.humidity }}%</p>
                <span>Humidity</span>
              </div>
            </div>
            <div class="d-flex justify-end align-center flex-grow-1 ">
              <v-icon icon="mdi-weather-dust" size="x-large" class="mr-5"></v-icon>
              <div>
                <p>{{ weatherData.windSpeed }} Km/h</p>
                <span>Wind Speed</span>
              </div>
            </div>
          </div>
        </v-col>
      </v-row>
    </v-card>
  </v-container>
</template>

<script setup>
import { useTheme } from 'vuetify'
import { ref, reactive, onMounted } from 'vue';

const theme = useTheme()

function toggleTheme () {
  theme.global.name.value = theme.global.current.value.dark ? 'light' : 'dark'
}

// Weather data icons
const icons = {
  "01d": 'https://openweathermap.org/img/wn/01d@4x.png',
  "02d": 'https://openweathermap.org/img/wn/02d@2x.png',
  "03d": 'https://openweathermap.org/img/wn/03d@2x.png',
  "04d": 'https://openweathermap.org/img/wn/04d@2x.png',
  "09d": 'https://openweathermap.org/img/wn/09d@2x.png',
  "10d": 'https://openweathermap.org/img/wn/10d@2x.png',
  "11d": 'https://openweathermap.org/img/wn/11d@2x.png',
  "13d": 'https://openweathermap.org/img/wn/13d@2x.png',
  "50d": 'https://openweathermap.org/img/wn/50d@2x.png',

  "01n": 'https://openweathermap.org/img/wn/01n@2x.png',
  "02n": 'https://openweathermap.org/img/wn/02n@2x.png',
  "03n": 'https://openweathermap.org/img/wn/03n@2x.png',
  "04n": 'https://openweathermap.org/img/wn/04n@2x.png',
  "09n": 'https://openweathermap.org/img/wn/09n@2x.png',
  "10n": 'https://openweathermap.org/img/wn/10n@2x.png',
  "11n": 'https://openweathermap.org/img/wn/11n@2x.png',
  "13n": 'https://openweathermap.org/img/wn/13n@2x.png',
  "50n": 'https://openweathermap.org/img/wn/50n@2x.png',
};

// State for weather data and input reference
const weatherData = reactive({
  location: '',
  icon: '',
  humidity: '',
  windSpeed: '',
  temperature: ''
});

const inputRef = ref(null);

// Function to search weather data for a city
const search = async (city) => {
  try {
    const url = `https://api.openweathermap.org/data/2.5/weather?q=${city}&appid=${import.meta.env.VITE_APP_ID}`;
    const response = await fetch(url);

    if (!response.ok) {
      throw new Error('City not found');
    }

    const data = await response.json();

    weatherData.location = data.name;
    weatherData.icon = data.weather[0].icon;
    weatherData.humidity = data.main.humidity;
    weatherData.windSpeed = data.wind.speed;
    weatherData.temperature = Math.floor(data.main.temp); // Convert Kelvin to Celsius
  } catch (error) {
    alert("City not found. Please try again.");
  }
};

// Handle form submit
const handleSubmit = () => {
  const city = inputRef.value?.value;
  if (city) {
    search(city);
  }
};

// Set default city weather on component mount
onMounted(() => {
  search('Philippines');
});
</script>
