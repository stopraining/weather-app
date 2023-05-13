<template>
  <div class="weather">
    <h1>Taiwan Weather</h1>
    <!-- <select name="" id="" v-model="temp" @change="getWeatherApi"> -->
    <div class="container-fluid">
      <select name="" id="" v-model="temp" @change="getWeatherApi" class="form-select-lg m-2" aria-label="Default select example">
        <option disabled value="" >縣市</option>
        <option v-for="item in locations" :value="item.code" >{{item.name}}</option>
      </select>
      <select name="" id="" v-model="area" @change="areaTest" class="form-select-lg" aria-label="Default select example">
        <option disabled value="" >鄉鎮區</option>
        <option v-for="(a,index ) in location" :value="index">{{ a.locationName }}</option>
      </select>
    </div>
    <br>
    
    <!-- area selected -->
    <div v-show="areaSelect.cardShow" class="container-fluid">
      <div class="row justify-content-center ">
        <div class="card mainCard">
          <div class="card-body">
              <div class="mainCardRight">
                <h2 class="card-title">{{areaSelect.name }}</h2>
                <i class="fa-solid fa-person fa-xl"></i>
                <span>&nbsp;{{ areaSelect.bodyTemp }} &#176;C</span><br>
                <i class="fa-solid fa-droplet fa-xl"></i>
                <span>&nbsp;{{ areaSelect.rain }}％</span>
              </div>
              <div class="mainCardLeft">
                <span>{{ areaSelect.intro }}</span>
                <i :class="this.areaSelect.icon"></i>
                <h2 class="temp">{{ areaSelect.temp }}&#176;C&nbsp;</h2>
              </div>
          </div>
        </div>
      </div>
    </div>
    <!-- all area -->
    <h1>總覽</h1>
    <div class="container-fluid">
      <div class="row row-cols-xxl-5 row justify-content-start g-4 p-5">
        <div class="col"  v-for="l in location">
          <div class="card allCard">
            <img src="" class="card-img-top" alt="">
            <div class="card-body">
              <h4>{{l.locationName }}</h4>
              <hr>  
              <p>{{l.weatherElement[1].time[1].elementValue[0].value }} </p><br>
              <i class="fa-solid fa-temperature-high"></i>
              <p>溫度：{{l.weatherElement[3].time[1].elementValue[0].value }} &#176;C</p><br>
              <i class="fa-solid fa-person"></i>
              <p>體感溫度：：{{l.weatherElement[2].time[1].elementValue[0].value }} &#176;C</p><br>
              <i class="fa-solid fa-droplet"></i>
              <p>降雨機率：{{l.weatherElement[7].time[0].elementValue[0].value }}%</p>
            </div>
          </div>
        </div>
      </div>
    </div>
    
   
  </div>
</template>

<script>
import axios from 'axios'
import moment from 'moment'
export default {
  name: 'Weather',
  data(){
    return{
      temp:'',
      apiKey:'CWB-CD62E333-DFC2-48D6-9D5F-B6CEE673C2EF',
      locations:[
        {name:'基隆市',code:'F-D0047-049'},
        {name:'新北市',code:'F-D0047-069'},
        {name:'臺北市',code:'F-D0047-061'},
        {name:'桃園市',code:'F-D0047-005'},
        {name:'新竹縣',code:'F-D0047-009'},
        {name:'新竹市',code:'F-D0047-053'},
        {name:'苗栗縣',code:'F-D0047-013'},
        {name:'臺中市',code:'F-D0047-073'},
        {name:'彰化縣',code:'F-D0047-017'},
        {name:'南投縣',code:'F-D0047-021'},
        {name:'雲林縣',code:'F-D0047-025'},
        {name:'嘉義縣',code:'F-D0047-029'},
        {name:'嘉義市',code:'F-D0047-057'},
        {name:'臺南市',code:'F-D0047-077'},
        {name:'高雄市',code:'F-D0047-065'},
        {name:'屏東縣',code:'F-D0047-033'},
        {name:'臺東縣',code:'F-D0047-037'},
        {name:'花蓮縣',code:'F-D0047-041'},
        {name:'宜蘭縣',code:'F-D0047-001'},
        {name:'澎湖縣',code:'F-D0047-045'},
        {name:'連江縣',code:'F-D0047-081'},
        {name:'金門縣',code:'F-D0047-085'},
      ],
      location:'',
      area:'',
      areaSelect:{name:'',temp:'',rain:'',intro:'',cardShow:false,icon:'',bodyTemp:''}
      

    }
  },
  methods:{
    async getWeatherApi(){
      this.areaSelect = {name:'',temp:'',intro:'',cardShow:false}
      this.area = ''
      let time = moment().format('YYYY-MM-DDTHH:mm:ss')
      time = encodeURIComponent(time)
      let getWeather = await axios.get(`https://opendata.cwb.gov.tw/api/v1/rest/datastore/${this.temp}?Authorization=${this.apiKey}&timeTo=${time}`)
      // let getWeather = await axios.get(`https://opendata.cwb.gov.tw/api/v1/rest/datastore/${this.temp}?Authorization=${this.apiKey}`)
      // console.log(JSON.stringify(getWeather))
      
      this.location = getWeather.data.records.locations[0].location
      
      // console.log(JSON.stringify(this.location))
    },
    areaTest(){ 
      
      // console.log(this.area)
      // console.log(this.location.length)

      // 選到的區域index
      let areaIndex = this.area 
      this.areaSelect.cardShow = true
      this.areaSelect.name = this.location[areaIndex].locationName
      this.areaSelect.temp = this.location[areaIndex].weatherElement[3].time[1].elementValue[0].value
      this.areaSelect.rain = this.location[areaIndex].weatherElement[7].time[0].elementValue[0].value
      this.areaSelect.intro= this.location[areaIndex].weatherElement[1].time[1].elementValue[0].value
      this.areaSelect.bodyTemp = this.location[areaIndex].weatherElement[2].time[1].elementValue[0].value
      
      if(/晴/g.test(this.areaSelect.intro)){
        this.areaSelect.icon = 'fa-solid fa-sun fa-2xl fa-bounce'
      }else if(/雨/g.test(this.areaSelect.intro)){
        this.areaSelect.icon = 'fa-solid fa-cloud-rain fa-2xl fa-bounce'
      }else if(/陰|雲/g.test(this.areaSelect.intro)){
        this.areaSelect.icon = 'fa-solid fa-cloud fa-2xl fa-bounce'
      }
      
      
      // return this.area===''?'':`${this.location[areaIndex].locationName}/溫度：${this.location[areaIndex].weatherElement[3].time[0].elementValue[0].value}`
      // return this.areaSelect.name
    }
  },
  props: {
    // areaIndex: Number,
    // locationData:Array
  }
  
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
/* <!-- <style lang="scss">
  @import '~bootstrap/scss/bootstrap'; --> */

h1{
  margin: 30px 0px; 
  color: white;
  opacity: 0.7;
}

h2 {
  margin: 0px; 
  font-size: 25pt;
  color:#002D6D;
}

ul {
  list-style-type: none;
  padding: 0;
}
li {
  /*display: inline-block;*/
  margin: 0 10px;
}
a {
  color:darkgreen;
}



.mainCard{
  width:350px; 
  color:black;
  background-color:white; 
  box-shadow: 0px 0px 10px #002D6D;;
  color: #0351C1;
  opacity: 0.7;
  text-align: left;
  /* background-color: transparent; */
}

.mainCard span{
  font-size: 15pt;
}

.mainCardRight{
  float: left;
  width: 50%;
}

.mainCardLeft{
  
  float: right;
  text-align: right;
  width: 50%;
}


.allCard{
  width:250px; 
  box-shadow: 5px 5px 10px white;
  color:steelblue
}

.allCard p{
  line-height: 12px;
  display: inline-block;
}

.temp{
  margin: 0px; 
  font-size: 40pt;
  
}

</style>
