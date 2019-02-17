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
          
{{ dataWeather.data.list[0].weather[0].icon }}
          <v-spacer></v-spacer>
          <p class="degree">&#176;</p>
           <v-btn-toggle v-model="toggle" class="transparent">
           <v-btn  width="40" class="pr-3 pl-3" :value="1" light >C</v-btn>
           <v-btn :value="2" class="pr-3 pl-3" light>F</v-btn>
           </v-btn-toggle>
        </v-card-title>
    </v-flex>
  </v-layout>


<v-container grid-list-xs  text-xs-center >
    <v-layout row wrap align-center justify-center >
      <v-flex xs4 sm4 md5 d-flex>
 
        <div > 
            <img src="../public/sun.png"  v-if="icon.Sun.value" height="230"/>
            <img src="../public/rain.png" v-if="icon.Rain.value" height="230"/>
            <img src="../public/storm.png" v-if="icon.Storm.value" height="230"/>
            <img src="../public/cloud.png" v-if="icon.Cloud.value" height="230"/>
            <img src="../public/partlyCloudy.png" v-if="icon.Partly.value" height="230"/> </div>
        
            <span class="infoNum">{{ this.Measure }}&#176;</span> <br>
            
      </v-flex>
  </v-layout>
 </v-container>
 
 <v-container fluid grid-list-sm text-xs-center>
    <v-layout row wrap justify-center align-center>
      <v-flex d-flex >
 <span class="Description">{{ dataWeather.data.list[0].weather[0].description }} </span>
</v-flex>
  </v-layout>
 </v-container>
 
  <v-container  grid-list-sm>
    <v-layout row wrap justify-space-around>
      <v-flex d-flex xs12 sm6 md2>
             <h3>Ветер</h3>
              <p>{{ dataWeather.data.list[0].wind.speed }}м.с {{ dataWeather.data.list[0].wind.deg }}</p>
      </v-flex>
      <v-flex d-flex xs12 sm6 md2>
        <v-layout row wrap>
          <v-flex d-flex>
           <h3>давление</h3>
            <p>{{ dataWeather.data.list[0].main.pressure }} мм рт.ст. </p>
          </v-flex>
        </v-layout>
      </v-flex>
      <v-flex d-flex xs12 sm6 md2>
      <h3>влажность</h3>
       <p> {{ dataWeather.data.list[0].main.humidity }} %  </p>
      </v-flex>
      <v-flex d-flex xs12 sm6 md2>
         <h3>Вероятность дождя </h3>
          <p v-if="dataWeather.data.list[0].rain > 0">{{ dataWeather.data.list[0].rain}} %</p>
          <p v-else>0 %</p>
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
     value_icon: [],
     value_icon2: [],
      icon:{
        Sun: {
         id: '01d',
         value: false,   
        },
        Rain: {
         id: '09d',
         value: false,   
        },
        Storm: {
         id: '11d',
         value: false,   
        },
        Cloud: {
         id: '03d',
         value: false,   
        },
        Partly: {
         id: '02d',
         value: false,   
        },
      }
      
    }
 },
 mounted() {
  
 },


   methods: {

    measureSystemC() {
      axios
       .get(`http://api.openweathermap.org/data/2.5/find?lat=${this.lat}&lon=${this.lon}&type=like&lang=ru&units=metric&APPID=${this.ApiKey}`)
       .then(response => (this.dataWeather = response))
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
    }

 },
  
  created() {
  this.getPosition();
   this.measureSystemC();
   this.measureSystemF();
   
 for(var key in this.icon) {
    this.value_icon.push(this.icon[key]);
}
 for (var i = 0; i < this.value_icon.length; i++) {
  if (this.value_icon[i].id == '09d'){
   console.log('this.icon[i].value') // доделать иконуи , не обращать на d, порезать строку 
}
  } 
  // тут были трудности , возращало undef... как не обращался, но нужно переделать код на map
  },
  
   computed: {
   Measure () {
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
