<template>
  <div class="container">
    <div class="card">
      <h1 class="text-center mb-20">🌤️ Türkiye Hava Durumu</h1>
      
      <!-- Şehir Arama -->
      <div class="flex mb-20">
        <select v-model="searchCity" class="input" style="margin-right: 10px; min-width: 200px;">
          <option v-for="city in cities" :key="city" :value="city">{{ city }}</option>
        </select>
        <button @click="searchWeather" class="btn">Ara</button>
      </div>

      <!-- Hata Mesajı -->
      <div v-if="error" class="error">
        {{ error }}
      </div>

      <!-- Yükleniyor -->
      <div v-if="loading" class="loading">
        Hava durumu bilgileri yükleniyor...
      </div>

      <!-- Mevcut Hava Durumu -->
      <div v-if="currentWeather" class="card">
        <div class="grid grid-2">
          <div class="text-center">
            <h2>{{ currentWeather.name }}, Türkiye</h2>
            <div class="temperature">{{ Math.round(currentWeather.main.temp) }}°C</div>
            <div class="description">{{ getWeatherDescription(currentWeather.weather[0].main) }}</div>
            <div class="details">
              Hissedilen: {{ Math.round(currentWeather.main.feels_like) }}°C
            </div>
          </div>
          <div class="text-center">
            <div class="weather-icon">
              {{ getWeatherIcon(currentWeather.weather[0].main) }}
            </div>
            <div class="details">
              <div>Nem: {{ currentWeather.main.humidity }}%</div>
              <div>Rüzgar: {{ Math.round(currentWeather.wind.speed * 3.6) }} km/s</div>
              <div>Basınç: {{ currentWeather.main.pressure }} hPa</div>
            </div>
          </div>
        </div>
      </div>

      <!-- Saatlik Tahmin -->
      <div v-if="hourlyForecast.length > 0" class="card">
        <h3 class="mb-20">⏰ Saatlik Tahmin</h3>
        <div class="grid grid-5">
          <div 
            v-for="(hour, index) in hourlyForecast.slice(0, 24)" 
            :key="index"
            class="text-center"
            style="padding: 15px; background: rgba(102, 126, 234, 0.1); border-radius: 12px;"
          >
            <div class="details">{{ formatHour(hour.dt) }}</div>
            <div class="weather-icon" style="width: 40px; height: 40px; margin: 10px auto;">
              {{ getWeatherIcon(hour.weather[0].main) }}
            </div>
            <div class="temperature-small">{{ Math.round(hour.main.temp) }}°C</div>
            <div class="details">{{ getWeatherDescription(hour.weather[0].main) }}</div>
          </div>
        </div>
      </div>

      <!-- 5 Günlük Tahmin -->
      <div v-if="dailyForecast.length > 0" class="card">
        <h3 class="mb-20">📅 5 Günlük Tahmin</h3>
        <div class="grid grid-3">
          <div 
            v-for="(day, index) in dailyForecast" 
            :key="index"
            class="text-center"
            style="padding: 20px; background: rgba(118, 75, 162, 0.1); border-radius: 12px;"
          >
            <div class="details">{{ formatDay(day.dt) }}</div>
            <div class="weather-icon" style="width: 50px; height: 50px; margin: 15px auto;">
              {{ getWeatherIcon(day.weather[0].main) }}
            </div>
            <div class="temperature-small">{{ Math.round(day.main.temp) }}°C</div>
            <div class="description">{{ getWeatherDescription(day.weather[0].main) }}</div>
            <div class="details">
              <div>Min: {{ Math.round(day.main.temp_min) }}°C</div>
              <div>Max: {{ Math.round(day.main.temp_max) }}°C</div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios'

export default {
  name: 'App',
  data() {
    return {
      searchCity: 'İstanbul',
      cities: [
        'İstanbul', 'Ankara', 'İzmir', 'Bursa', 'Adana', 'Antalya', 'Konya', 'Gaziantep',
        'Şanlıurfa', 'Kocaeli', 'Mersin', 'Kayseri', 'Eskişehir', 'Diyarbakır', 'Samsun',
        'Denizli', 'Sakarya', 'Erzurum', 'Trabzon', 'Malatya', 'Manisa', 'Balıkesir',
        'Van', 'Aydın', 'Tekirdağ', 'Hatay', 'Ordu', 'Afyonkarahisar', 'Elazığ', 'Sivas',
        'Kahramanmaraş', 'Batman', 'Çanakkale', 'Kütahya', 'Isparta', 'Osmaniye', 'Edirne',
        'Aksaray', 'Düzce', 'Yalova', 'Karabük', 'Kilis', 'Bartın', 'Ardahan', 'Iğdır',
        'Sinop', 'Bayburt', 'Gümüşhane', 'Bilecik', 'Uşak', 'Amasya', 'Kırıkkale', 'Kırşehir',
        'Nevşehir', 'Niğde', 'Kastamonu', 'Rize', 'Artvin', 'Bolu', 'Tokat', 'Çorum',
        'Zonguldak', 'Karaman', 'Kırklareli', 'Burdur', 'Muğla', 'Mardin', 'Şırnak',
        'Hakkari', 'Bitlis', 'Siirt', 'Ağrı', 'Muş', 'Bingöl', 'Tunceli', 'Erzincan',
        'Giresun', 'Yozgat', 'Çankırı', 'Bartın', 'Ardahan', 'Iğdır', 'Sinop', 'Bayburt',
        'Gümüşhane', 'Bilecik', 'Uşak', 'Amasya', 'Kırıkkale', 'Kırşehir', 'Nevşehir',
        'Niğde', 'Kastamonu', 'Rize', 'Artvin', 'Bolu', 'Tokat', 'Çorum', 'Zonguldak',
        'Karaman', 'Kırklareli', 'Burdur', 'Muğla', 'Mardin', 'Şırnak', 'Hakkari',
        'Bitlis', 'Siirt', 'Ağrı', 'Muş', 'Bingöl', 'Tunceli', 'Erzincan', 'Giresun',
        'Yozgat', 'Çankırı'
      ],
      currentWeather: null,
      hourlyForecast: [],
      dailyForecast: [],
      loading: false,
      error: null,
      apiKey: 'YOUR_API_KEY', // OpenWeatherMap API anahtarınızı buraya ekleyin
      demoMode: true // Demo modu aktif - API anahtarı için: https://openweathermap.org/api
    }
  },
  mounted() {
    this.getWeather()
  },
  methods: {
    async getWeather() {
      this.loading = true
      this.error = null
      
      try {
        if (this.demoMode) {
          // Demo veriler - şehre göre farklı hava durumları
          const cityTemps = {
            'İstanbul': { temp: 18, feels_like: 20, humidity: 75 },
            'Ankara': { temp: 12, feels_like: 14, humidity: 60 },
            'İzmir': { temp: 22, feels_like: 24, humidity: 65 },
            'Bursa': { temp: 16, feels_like: 18, humidity: 70 },
            'Adana': { temp: 25, feels_like: 27, humidity: 55 },
            'Antalya': { temp: 23, feels_like: 25, humidity: 60 },
            'Konya': { temp: 10, feels_like: 12, humidity: 50 },
            'Gaziantep': { temp: 20, feels_like: 22, humidity: 45 },
            'Şanlıurfa': { temp: 28, feels_like: 30, humidity: 40 },
            'Kocaeli': { temp: 17, feels_like: 19, humidity: 72 }
          }
          
          const cityData = cityTemps[this.searchCity] || { temp: 20, feels_like: 22, humidity: 65 }
          
          this.currentWeather = {
            name: this.searchCity,
            main: {
              temp: cityData.temp,
              feels_like: cityData.feels_like,
              humidity: cityData.humidity,
              pressure: 1013
            },
            weather: [{ main: this.getRandomWeather() }],
            wind: { speed: 3.5 }
          }
          
          // Demo saatlik tahmin - daha gerçekçi
          this.hourlyForecast = Array.from({ length: 24 }, (_, i) => {
            const hour = new Date()
            hour.setHours(hour.getHours() + i)
            const isNight = hour.getHours() < 6 || hour.getHours() > 20
            const baseTemp = cityData.temp + (isNight ? -3 : 2)
            
            return {
              dt: hour.getTime() / 1000,
              main: {
                temp: baseTemp + Math.sin(i / 6) * 3,
                temp_min: baseTemp - 2 + Math.sin(i / 6) * 2,
                temp_max: baseTemp + 3 + Math.sin(i / 6) * 2
              },
              weather: [{ main: this.getRandomWeather() }]
            }
          })
          
          // Demo günlük tahmin
          this.dailyForecast = Array.from({ length: 5 }, (_, i) => {
            const day = new Date()
            day.setDate(day.getDate() + i)
            
            return {
              dt: day.getTime() / 1000,
              main: {
                temp: cityData.temp + Math.sin(i / 2) * 4,
                temp_min: cityData.temp - 3 + Math.sin(i / 2) * 3,
                temp_max: cityData.temp + 5 + Math.sin(i / 2) * 3
              },
              weather: [{ main: this.getRandomWeather() }]
            }
          })
          
          // Demo modu uyarısı
          this.error = '⚠️ Demo modu aktif. Gerçek veriler için ücretsiz API anahtarı alın: https://openweathermap.org/api'
        } else {
          // Gerçek API çağrıları
          const currentResponse = await axios.get(
            `https://api.openweathermap.org/data/2.5/weather?q=${this.searchCity},TR&appid=${this.apiKey}&units=metric&lang=tr`
          )
          this.currentWeather = currentResponse.data

          const forecastResponse = await axios.get(
            `https://api.openweathermap.org/data/2.5/forecast?q=${this.searchCity},TR&appid=${this.apiKey}&units=metric&lang=tr`
          )
          
          this.hourlyForecast = forecastResponse.data.list
          
          const dailyData = {}
          forecastResponse.data.list.forEach(item => {
            const date = new Date(item.dt * 1000).toDateString()
            if (!dailyData[date]) {
              dailyData[date] = item
            }
          })
          this.dailyForecast = Object.values(dailyData).slice(0, 5)
        }
        
      } catch (error) {
        this.error = 'Hava durumu bilgileri alınamadı. Lütfen şehir adını kontrol edin.'
        console.error('Hava durumu hatası:', error)
      } finally {
        this.loading = false
      }
    },
    
    searchWeather() {
      if (this.searchCity.trim()) {
        this.getWeather()
      }
    },
    
    getWeatherIcon(weatherMain) {
      const icons = {
        'Clear': '☀️',
        'Clouds': '☁️',
        'Rain': '🌧️',
        'Drizzle': '🌦️',
        'Thunderstorm': '⛈️',
        'Snow': '❄️',
        'Mist': '🌫️',
        'Smoke': '🌫️',
        'Haze': '🌫️',
        'Dust': '🌫️',
        'Fog': '🌫️',
        'Sand': '🌫️',
        'Ash': '🌫️',
        'Squall': '💨',
        'Tornado': '🌪️'
      }
      return icons[weatherMain] || '🌤️'
    },
    
    getWeatherDescription(weatherMain) {
      const descriptions = {
        'Clear': 'Açık',
        'Clouds': 'Bulutlu',
        'Rain': 'Yağmurlu',
        'Drizzle': 'Hafif Yağmurlu',
        'Thunderstorm': 'Gök Gürültülü',
        'Snow': 'Karlı',
        'Mist': 'Sisli',
        'Smoke': 'Dumanlı',
        'Haze': 'Puslu',
        'Dust': 'Tozlu',
        'Fog': 'Sisli',
        'Sand': 'Kumlu',
        'Ash': 'Kül',
        'Squall': 'Fırtınalı',
        'Tornado': 'Kasırga'
      }
      return descriptions[weatherMain] || 'Bilinmiyor'
    },
    
    formatHour(timestamp) {
      const date = new Date(timestamp * 1000)
      return date.toLocaleTimeString('tr-TR', { 
        hour: '2-digit', 
        minute: '2-digit' 
      })
    },
    
    formatDay(timestamp) {
      const date = new Date(timestamp * 1000)
      return date.toLocaleDateString('tr-TR', { 
        weekday: 'long',
        day: 'numeric',
        month: 'short'
      })
    },
    
    getRandomWeather() {
      const weathers = ['Clear', 'Clouds', 'Rain', 'Drizzle', 'Thunderstorm', 'Snow', 'Mist', 'Smoke', 'Haze', 'Dust', 'Fog', 'Sand', 'Ash', 'Squall', 'Tornado']
      return weathers[Math.floor(Math.random() * weathers.length)]
    }
  }
}
</script> 