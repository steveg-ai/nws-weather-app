


<template>

  <div id="main">

    <div class="container my-5">
      <h1 class="text-center title">Enter Location</h1>
      <form v-on:submit.prevent="getWeatherInfo" >
      <input
        type="text"
        placeholder="Please Enter a city in Idaho? (city, ID)"
        v-model="cityFind"
        autocomplete="off"
        class="form-control text-muted form-rounded p-4 shadow-sm"
      />
    </form>


    <div class="card rounded my-3 shadow-lg back-card overflow-hidden">
      <div class="card-top text-center" style="margin-bottom: 5rem">
        <div class="city-name my-3">
          <p>{{ weather.cityName }}</p>
          <span>.......</span>
        </div>
      </div>

      <div class="card-body">
        <div class="card-mid">
          <div class="row">
            <div class="col-12 text-center forecast">
              <span>{{ weather.today }}  {{ weather.todayTemp }}&deg;F</span>
            </div>
            <span>.....</span>
            <span>..... </span>
            <div class="col-12 text-center forecast">
              <span>{{ weather.tonight }}  {{ weather.tonightTemp }}&deg;F</span>
            </div>
          </div>
          <div class="row">
            <div class="col-md-12 text-center">
              <p class="my-5">{{ weather.todayDescription }}</p>
            </div>
          </div>

          <div class="row">
            <div class="col-12 text-center forecast">
              <span>{{ weather.tomorrow }}  {{ weather.tomorrowTemp }}&deg;F</span>
            </div>
            <span>.....</span>
            <span>..... </span>
            <div class="col-12 text-center forecast">
              <span>{{ weather.tomorrowNight }}  {{ weather.tomorrowNightTemp }}&deg;F</span>
            </div>
          </div>
          <div class="row">
            <div class="col-md-12 text-center">
              <p class="my-5">{{ weather.tomorrowDescription }}</p>
            </div>
          </div>

        </div>
      </div>
    </div>
  </div>
</div> 
</template >


  <script>
  export default {
      //use the Vue.js built-in data() method to do two things:
      // 1) Bind the data with the HTML (Here, the data is the weather information)
      // 2) Create a method to call the api and capture/pars the return data
      // Note: data() is a built-in function that returns an object for Vue. From within
      // this object we refer to data using the keyword "this." attached to the data.
      data() {
        return {
          cityFind: "",
          weather: {
            cityName:"No City (default)",

            todayDescription: "Weather short forecast",
            todayTemp: 0,
            today: "",
            tonightTemp: 0,
            tonight: "",

            tomorrowDescription: "Weather short forecast",            
            tomorrowTemp: 0,
            tomorrow: "",
            tomorrowNightTemp: 0,
            tomorrowNight: "",
    
         },
       };
     },
    methods: {
      getWeatherInfo: async function(){
          console.log('User input:', this.cityFind)
          //Use this api to get lat/lon from city input
          const coordURL = 'http://api.openweathermap.org/geo/1.0/direct?q=' + this.cityFind + ',US&appid=96a913d201298e80c408abe3c8ab3eab'
          const coordResult = await fetch(coordURL);//call api with URL and key
          const coordData = await coordResult.json()//convert json file to an object called data
             console.log('Returned Lat/Lon Data from api and user input:', coordData);//log the data object to the console to see the returned data
          const lat = coordData[0].lat;
          const lon = coordData[0].lon;
             console.log('Returned latitude: ',lat)
             console.log('Returned longitude: ',lon)
          this.weather.cityName = coordData[0].name
          const gridPointURL = 'https://api.weather.gov/points/'+ lat + ',' + lon
             console.log('URL for NWS grid points api: ', gridPointURL)
          const gridPointResult = await fetch(gridPointURL)
          const gridPointData = await gridPointResult.json()
             console.log('Returned grid point data from NWS api: ', gridPointData)
             console.log('NWS Grid Point X: ', gridPointData.properties.gridX)
             console.log('NWS Grid Point Y: ', gridPointData.properties.gridY)
          const x = gridPointData.properties.gridX;
          const y = gridPointData.properties.gridY;
          const weatherURL = 'https://api.weather.gov/gridpoints/BOI/' + x + ',' + y + '/forecast?units=us';
          const weatherResult = await fetch(weatherURL);//call api with URL and key
          const weatherData = await weatherResult.json()//convert json file to an object called data
             console.log('Returned weather data from NWS api: ', weatherData);//log the data object to the console to see the returned data
          
          this.weather.todayDescription = weatherData.properties.periods[0].detailedForecast;
          this.weather.todayTemp = Math.trunc(weatherData.properties.periods[0].temperature);
          this.weather.today = weatherData.properties.periods[0].name;
          this.weather.tonightTemp = Math.trunc(weatherData.properties.periods[1].temperature);
          this.weather.tonight = weatherData.properties.periods[1].name;
      
          this.weather.tomorrowDescription = weatherData.properties.periods[2].detailedForecast;
          this.weather.tomorrowTemp = Math.trunc(weatherData.properties.periods[2].temperature);
          this.weather.tomorrow = weatherData.properties.periods[2].name;
          this.weather.tomorrowNightTemp = Math.trunc(weatherData.properties.periods[1].temperature);
          this.weather.tomorrowNight = weatherData.properties.periods[3].name;

      },
    },
  };

  </script >

  <style>
    @import "./assets/custom.css";
  </style>
