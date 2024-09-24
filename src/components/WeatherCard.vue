<template>
    <div class="container">
        <div class="card">
            <div class="card-header">
                <h2>Enter City</h2>
            </div>
            <div class="card-body">
                <input v-model="city" placeholder="Enter city name" />
                <button @click="refreshWeather">Refresh</button>
                <p v-if="errorMessage" class="error">{{ errorMessage }}</p>
            </div>
        </div>
        <div id="hidden" v-if="weatherData">
            <div class="card-header">
                <h2>Weather in {{ city }}</h2>
            </div>
            <div class="card-body">
                <img v-if="weatherImageUrl" :src="weatherImageUrl" alt="Weather icon" class="weather-icon" />
                <i :class="weatherIconClass" class="fa fa-cloud-rain"></i>
                <p>Temperature: {{ (weatherData.main.temp - 273.15).toFixed(2) }}°C</p>
                <p>Humidity: {{ weatherData.main.humidity }}%</p>
                <p>Wind: {{ weatherData.wind.speed }} km/h</p>
                <p v-if="weatherMessage">{{ weatherMessage }}</p>
            </div>
        </div>
    </div>
</template>

<script setup>
import { ref, computed, onMounted } from 'vue'

const city = ref('Burao');
const weatherData = ref(null);
const errorMessage = ref('');
const hidden = ref(false);

const fetchWeatherData = async (city) => {
    const apiKey = '69223652d91d48197460a43719218459';
    const url = `https://api.openweathermap.org/data/2.5/weather?q=${city}&appid=${apiKey}`;

    try {
        const response = await fetch(url);
        if (!response.ok) {
            throw new Error('Network response was not ok');
        }
        const data = await response.json();
        weatherData.value = data;
        errorMessage.value = ''; // Clear any previous error message
    } catch (error) {
        console.error('There has been a problem with your fetch operation:', error);
        errorMessage.value = 'Please write a valid city name';
        weatherData.value = null; // Clear weather data if fetch fails
    }
};

const refreshWeather = () => {
    fetchWeatherData(city.value);
};

// Computed property to determine the weather message
const weatherMessage = computed(() => {
    if (!weatherData.value) return '';
    const tempCelsius = weatherData.value.main.temp - 273.15;
    const weatherCondition = weatherData.value.weather[0].main.toLowerCase();

    if (tempCelsius < 15) {
        return "It's cold!";
    } else if (weatherCondition.includes('rain')) {
        return "It's raining!";
    } else if (weatherCondition.includes('wind')) {
        return "It's windy!";
    } else if (weatherCondition.includes('snow')) {
        return "It's snowing!";
    } else if (weatherCondition.includes('cloud')) {
        return "It's cloudy!";
    } else if (weatherCondition.includes('mist')) {
        return "It's misty!";
    } else if (weatherCondition.includes('clear')) {
        return "The sky is clear!";
    } else if (tempCelsius > 30) {
        return "It's hot!";
    }
    else {
        return "The weather is nice!";
    }

});

// Computed property to determine the weather image URL
const weatherImageUrl = computed(() => {
    if (!weatherData.value) return '';
    const weatherCondition = weatherData.value.weather[0].main.toLowerCase();

    if (weatherCondition.includes('rain')) {
        return require('@/assets/images/rain.png');
    } else if (weatherCondition.includes('wind')) {
        return require('@/assets/images/wind.png');
    } else if (weatherCondition.includes('snow')) {
        return require('@/assets/images/snow.png');
    } else if (weatherCondition.includes('cloud')) {
        return require('@/assets/images/cloud.png');
    } else if (weatherCondition.includes('mist')) {
        return require('@/assets/images/mist.png');
    } else if (weatherCondition.includes('clear')) {
        return require('@/assets/images/clear.png');
    }
     else {
        return require('@/assets/images/404.png');
    }

});

if(hidden == true){
    document.getElementById('hidden').style.display = 'block';
}
// Fetch weather data on component mount
onMounted(() => {
    fetchWeatherData(city.value);
});
</script>
<style scoped>
.container {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    margin-top: 20px;
    background: linear-gradient(135deg, #89f7fe 0%, #66a6ff 100%);
    min-height: 100vh;
    padding: 20px;
    box-sizing: border-box;
    font-family: 'Roboto', sans-serif;
}

.card, #hidden {
    width: 100%;
    max-width: 350px;
    border: none;
    border-radius: 12px;
    box-shadow: 0 8px 16px rgba(0, 0, 0, 0.2);
    overflow: hidden;
    margin-bottom: 20px;
    background: rgba(255, 255, 255, 0.2);
    backdrop-filter: blur(15px);
    -webkit-backdrop-filter: blur(15px);
    padding: 20px;
    transition: transform 0.3s ease;
}

.card:hover, #hidden:hover {
    transform: translateY(-10px);
}

.card-header {
    background-color: rgba(0, 123, 255, 0.9);
    color: white;
    padding: 15px;
    text-align: center;
    font-size: 1.4em;
    font-weight: bold;
    border-radius: 12px 12px 0 0;
}

.card-body {
    padding: 20px;
    text-align: center;
}

.weather-icon, #hidden img {
    width: 60px;
    height: 60px;
    margin-bottom: 15px;
    opacity: 0.8;
}

input {
    padding: 10px;
    margin-right: 10px;
    border: 1px solid #ccc;
    border-radius: 8px;
    width: calc(100% - 100px); /* Adjust width to fit with button */
    box-sizing: border-box;
    font-size: 1em;
}

button {
    padding: 10px 15px;
    border: none;
    background-color: #28a745;
    color: white;
    border-radius: 8px;
    cursor: pointer;
    transition: background-color 0.3s ease, transform 0.3s ease;
    font-size: 1em;
}

button:hover {
    background-color: #1e7e34; /* Darker shade of green */
    transform: translateY(-2px);
}

.error {
    color: red;
    margin-top: 10px;
    font-size: 0.9em;
}

/* Responsive styles */
@media (max-width: 600px) {
    .card, #hidden {
        max-width: 100%;
        padding: 15px;
    }

    input {
        width: 100%;
        margin-bottom: 10px;
    }

    button {
        width: 100%;
    }
}
</style>
