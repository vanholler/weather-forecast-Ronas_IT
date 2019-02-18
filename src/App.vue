<template>
  <v-app>
  <v-content>

  <v-layout row>
    <v-flex xs12 sm12 >
        <v-card-title class=" black--text">
          <v-layout row wrap>
          <v-flex d-flex xs12 sm6 md5>
            <v-card color="#fff" >
              <v-card-text>{{ transliterate(dataWeather.data.list[0].name, true)}}</v-card-text>
            </v-card>
          </v-flex>
          </v-layout>
          <v-spacer></v-spacer>
          <p class="degree">&#176;</p>
            <v-btn-toggle v-model="toggle" class="transparent">
            <v-btn  width="40" class="pr-3 pl-3" :value="1" light >C</v-btn>
            <v-btn :value="2" class="pr-3 pl-3" light>F</v-btn>
            </v-btn-toggle>
        </v-card-title>
    </v-flex>
  </v-layout>


  <v-container grid-list-xs  text-xs-center mt-5>
      <v-layout row wrap align-center justify-center >
        <v-flex xs4 sm4 md5 d-flex>
          <div> 
            <img src="../public/sun.png"  v-if="icon[0].value" height="230"/>
            <img src="../public/rain.png" v-if="icon[1].value" height="230"/>
            <img src="../public/storm.png" v-if="icon[2].value" height="230"/>
            <img src="../public/cloud.png" v-if="icon[3].value" height="230"/>
            <img src="../public/partlyCloudy.png" v-if="icon[4].value" height="230"/>
          </div>
          <span class="infoNum">{{ this.Measure }}&#176;</span> <br>
        </v-flex>
    </v-layout>
   </v-container>
 
   <v-container fluid grid-list-sm text-xs-center>
      <v-layout row wrap justify-center align-center>
      <v-flex d-flex>
         <div class="Description headline font-weight-medium">{{ dataWeather.data.list[0].weather[0].description }}</div>
      </v-flex>
      </v-layout>
   </v-container>
 
 
   <v-container  grid-list-lg mt-5>
     <v-layout row wrap justify-space-around white--text>
       <v-flex d-flex xs12 sm6 md2>
           <div class="title font-weight-regular" >Ветер:</div>
           <div class="subheading font-weight-regular">{{ dataWeather.data.list[0].wind.speed }}м.с</div>
       </v-flex>
       <v-flex d-flex xs12 sm6 md3>
       <v-layout align-center row >
         <v-flex d-flex>
           <div class="title font-weight-regular">Давление:</div>
           <div class="subheading font-weight-regular"> {{ dataWeather.data.list[0].main.pressure }} мм рт.ст.</div>
         </v-flex>
       </v-layout>
         </v-flex>
       <v-flex d-flex xs12 sm6 md2>
           <div class="title font-weight-regular">Влажность:</div>
           <div class="subheading font-weight-regular"> {{ dataWeather.data.list[0].main.humidity }} %  </div>
       </v-flex>
       <v-flex d-flex xs12 sm6 md3>
           <div class="title font-weight-regular">Вероятность дождя: </div>
           <div 
           class="subheading font-weight-regular" 
           v-if="dataWeather.data.list[0].rain > 0">
           {{ dataWeather.data.list[0].rain}} %</div>
           <div class="titsubheadingle font-weight-regular" v-else>0%</div>
       </v-flex>
     </v-layout>
   </v-container>

  
  </v-content>
  </v-app>
</template>


<script>
import axios from 'axios';

export default {
  name: 'App',
  data () {
    return {
      toggle: 1,
      ApiKey: 'fa51662fc45090a903937aa9ec6a97ce',
      dataWeather: '',
      measure_fahrenheit: '',
      temperature:'',
      lat: '45',
      lon: '38',
      value_icon: '',
      value_icon2: [],
       icon:[
          {
          name: 'Sun',
          id: '01d',
          value: false,   
         },
      {
          name: 'Rain',
          id: '09d',
          value: false,   
         },
         {
          name: 'Storm',
          id: '11d',
          value: false,   
         },
        {
          name: 'Cloud',
          id: '03d',
          value: false,   
         },
        {
          name: 'Partly',
          id: '02d',
          value: false,   
         },
  ]
      
    }
   },

   methods: {

    measureSystemC() {
      axios
       .get(`http://api.openweathermap.org/data/2.5/find?lat=${this.lat}&lon=${this.lon}&type=like&lang=ru&units=metric&APPID=${this.ApiKey}`)
       .then(response => (this.dataWeather = response))
      .then(response => (this.value_icon = response.data.list[0].weather[0].icon.slice(0, 2)))
     
    },
    measureSystemF() {
     axios
      .get(`http://api.openweathermap.org/data/2.5/find?lat=${this.lat}&lon=${this.lon}&type=like&lang=ru&units=Imperial&APPID=${this.ApiKey}`)
      .then(response => (this.measure_fahrenheit = response))

    },
    getPosition: function() {
        navigator.geolocation.getCurrentPosition(this.updatePosition);

    },
    updatePosition: function(position) {
        this.lat = position.coords.latitude;
        this.lon = position.coords.longitude;
    },
    
    transliterate: function(text, engToRus) {
         var
            rus = "щ   ш  ч  ц  ю  я  ё ой  ж  ъ  ы  э  а б в г д е з и й к л м н о п р с т у ф х ь".split(/ +/g),
            eng = "shh sh ch cz yu ya yo oy zh `` y' e` a b v g d e z i j k l m n o p r s t u f x `".split(/ +/g),
             x;
            for(x = 0; x < rus.length; x++) {
                text = text.split(engToRus ? eng[x] : rus[x]).join(engToRus ? rus[x] : eng[x]);
                text = text.split(engToRus ? eng[x].toUpperCase() : rus[x].toUpperCase()).join(engToRus ? rus[x].toUpperCase() : eng[x].toUpperCase());
            }
            return text;
    }, // транслитерация на кириллицу , т.к. Api предоставляет название городов только в латинице.

    toggleIcon: function() {
      return new Promise((resolve, reject) => {
        setTimeout(() => {
          for(var key in this.icon) {
          if (this.icon[key].id.slice(0,2) == this.value_icon) {
          this.icon[key].value = true;
          }  
          } 
          resolve();
        }, 2000); 
      });
    } // тут у меня появились проблемы с асинхронностью. потратил много времени. i'm so sorry =(
 },
  
 async created() {
   await this.getPosition();
   await this.measureSystemC();
   await this.measureSystemF();
   await this.toggleIcon();
},
  
   computed: {
      
     Measure () {  
       // при инициализации сразу делаю 2 api запроса и сохраняю нужные данные, т.к. при рективном переключении, на 2 запрос нужно время и это визуально заметно было 
       if (this.toggle === 1) {
        return this.temperature = this.dataWeather.data.list[0].main.temp
       } else {
       if (this.toggle === 2) {
         return this.temperature = this.measure_fahrenheit.data.list[0].main.temp
       }
       }
       if(this.toggle === undefined) {
         return this.temperature = this.dataWeather.data.list[0].main.temp
       }  
     },    

   }
}
</script>

<style>
 
#app {
   background-color: #498CEC;
}
 
.degree {
  font-size: 20px;
  padding-right: 10px;
}
 .infoNum {
  font-size: 10vw;
  color: #fff;
  background: none;
 }
 .Description {
  color: #fff;
  font-size: 2vw;
 }
</style>
