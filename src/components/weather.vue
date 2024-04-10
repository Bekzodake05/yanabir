<template>
  <div class="container1">
    <div class="timedisplay">
      <div class="hour">{{ hm }}</div>
      <div class="wd">{{ wd }}</div>
      <div class="shahar">{{ data.capname }}</div>
    </div>
    <div class="cnamedisplay">
      <form v-on:submit.prevent="getdata">
        <input type="text" list="shahar" v-model="cityname">
        <datalist id="shahar">
          <option value="Tashkent">Toshkent</option>
          <option value="Buxoro">Buxoro</option>
          <option value="Samarqand">Samarqand</option>
          <option value="Navoiy">Navoiy</option>
          <option value="Namangan">Namangan</option>
          <option value="Andijon">Andijon</option>
          <option value="Farg'ona">Farg'ona</option>
          <option value="Kashkadaryo">Kashkadaryo</option>
          <option value="Sirdaryo">Sirdaryo</option>
          <option value="Jizzax">Jizzax</option>
          <option value="Nukus">Nukus</option>
        </datalist>
        <button id="btn" type="submit"><img :src="imagepath" alt="s"></button>
      </form>
      <div class="current">
        <div class="hobhavo cont">
          <p>Ob-havo</p>
          <img :src="data.foricon" alt="icon">
        </div>
        <div class="namlik cont">
          <p>Temperatura</p>
          <p>{{ data.currentw }}</p>
        </div>
        
        <div class="shamol cont">
          <p>Shamol tezligi</p>
          <p>{{ data.windspeed }}</p>
        </div>
        <div class="namlik cont">
          <p>Quyosh chiqishi</p>
          <p>{{ data.qchiq }}</p>
        </div>
        <div class="namlik cont">
          <p>Quyosh botishi</p>
          <p>{{ data.qbot }}</p>
        </div>
        <div class="namlik cont">
          <p>Namlik</p>
          <p>{{ data.humidity }}%</p>
        </div>
      </div>
      
    </div>
  </div>
  <h1 class="yana">2 haftalik ob-havo</h1>
  <div class="hourdata">
    <table>
      <tr>
        <th>Kun</th>
        <th>Temperatura</th>
        <th>Shamol tezligi</th>
        <th>Tavsif</th>
      </tr>
      <tr v-for="dai in daily" :key="dai">
        <td class="double"><p>{{ dai.weekday }} </p><p class="second">{{ dai.tablesana }}</p></td>
        <td>{{ dai.wtemp }}</td>
        <td>{{ dai.wind }}</td>
        <td><img :src="dai.wicon" alt="icon"></td>
      </tr>
    </table>
  </div>
  <div class="oxirgi">
    <h1 class="itis" :style="{ visibility: tableshow ? 'visible' : 'hidden' }">Sana: {{ yanasana }}</h1>
    <div class="inputcontainer">
      <p>Soatlik ob-havoni olish uchun sananani kiriting:</p>
      <form>
        <input style="float: right;" id="hourdata" type="date" :min="mindate" :max="maxdate" v-model="hourlyweather" @input="gethour">
      </form>
    </div>
    
  </div>    
    
  <div class="hourdata">
    <table class="table2" v-show="tableshow">
      <tr>
        <th>Vaqt</th>
        <th>Temperatura</th>
        <th>Shamol tezligi</th>
        <th>Tavsif</th>
      </tr>
      <tr v-for="hourlyw in hourl" :key="hourlyw">
        <td >{{ hourlyw.hour }}</td>
        <td>{{ hourlyw.htemp }}</td>
        <td>{{ hourlyw.hspeed }}</td>
        <td><img :src="hourlyw.hicon" alt="icon"></td>
      </tr>
    </table>
  </div>
    
  
</template>


<script >
import { DateTime } from "luxon";

export default{

  data(){
    return{
      windowWidth: window.innerWidth,
      windowHeight: window.innerHeight,
      imagepath: require('./pictures/download.svg'),
      tun: require('./pictures/1.svg'),
      cloudy: require('./pictures/2.svg'),
      sun: require('./pictures/3.svg'),
      rain: require('./pictures/4.svg'),
      snow: require('./pictures/5.svg'),
      thunder: require('./pictures/thunder.svg'),
      day: require('./pictures/day.svg'),
      night: require('./pictures/night.png'),
      nightcloudy: require('./pictures/nightcloudy.png'),
      nightsnow: require('./pictures/nightsnow.png'),
      nightrain: require('./pictures/nightrain.png'),
      cityname: "",
      defaultcityname:  "Tashkent",
      forhourdata: [],
      data: [],
      daily: [],
      hourl: [],
      city: "",
      hm: "",
      wd: "",
      mindate: "",
      maxdate: "",
      hourlyweather: "",
      tableshow: false,
      yanasana: "",
    }
  },
  mounted(){
    this.getdata()
  },

  methods: {

    selsiy(temp){
      let seltemp=(Math.floor((temp-32)*5)/9).toFixed(1);
      return `${seltemp} °C`;

    },
    gethour(){
      this.yanasana=this.monthdate(this.hourlyweather)
      this.tableshow=true;
      let index;
      for(let i=1;i<=14;i++){
        if(this.forhourdata[i].datetime==this.hourlyweather){
          index=i;
        }
      }
      this.hourl=[];
        for(let i=1; i<24;i+=3){
          let iconify;
          if(this.forhourdata[index].hours[i].datetime>this.forhourdata[index].sunrise && this.forhourdata[index].hours[i].datetime<this.forhourdata[index].sunset){
            iconify=this.geticon(this.forhourdata[index].hours[i].icon);
          }
          else{
            iconify=this.getnighticon(this.forhourdata[index].hours[i].icon)
          }
          let htemperatura=this.selsiy(this.forhourdata[index].hours[i].temp)
          let aobject={
              hour: this.forhourdata[index].hours[i].datetime.slice(0,-3),
              htemp: htemperatura,
              hspeed: this.forhourdata[index].hours[i].windspeed,
              hicon: iconify
          }
          this.hourl.push(aobject)
      }
    },
    monthdate(dat){
      let sliceddate=dat.split('-');
      let numofoy= sliceddate[1];
      const oylar= {
        '01':'Yanvar',
        '02':'Fevral',
        '03':'Mart',
        '04':'Aprel',
        '05':'May',
        '06':'Iyun',
        '07':'Iyul',
        '08':'Avgust',
        '09':'Sentabr',
        '10':'Oktabr',
        '11':'Noyabr',
        '12':'Dekabr',
      }
      let uzbsana=sliceddate[2];
      if(uzbsana.charAt(0)=='0'){
        uzbsana= uzbsana.substring(1)
      }

      const oy=oylar[numofoy]
      const sana= `${uzbsana}-${oy}`
      return sana;
     
    },
    getnighticon(nighticons){
      let nighticonify=nighticons;

      if(nighticonify.includes('clear')){
      nighticonify= this.night;
      }
      else if(nighticonify.includes('rain')){
        nighticonify= this.nightrain;
      }
      else if(nighticonify.includes('snow')){
        nighticonify=this.nightsnow;
      }
      else{
        nighticonify = this.nightcloudy
      }
      return nighticonify;

    },
    geticon(icons){
      let iconify=icons;

      if(iconify.includes('clear')){
      iconify= this.sun;
      }
      else if(iconify=='rain'){
        iconify= this.rain;
      }
      else if(iconify.includes('thunderstorm')){
        iconify=this.thunder;
      }
      else{
        iconify = this.cloudy
      }
      return iconify;

    },
    hkuns(enday){

    const haftakunlar = {
        'Mon': 'Dushanba',
        'Tue': 'Seshanba',
        'Wed': 'Chorshanba',
        'Thu': 'Payshanba',
        'Fri': 'Juma',
        'Sat': 'Shanba',
        'Sun': 'Yakshanba'
      };
      return haftakunlar[enday]
    },

    getTime(timezone) {
        if (timezone) {
          const now = DateTime.now().setZone(timezone);
          const dateFormatted = now.toFormat("d-LLLL"); 
          const dayofweek = now.toFormat("EEE")
          const timeFormatted = now.toFormat("HH:mm"); 
          const hkun = this.hkuns(dayofweek)
          this.hm = `${timeFormatted}`;
          this.wd = `${hkun}, ${dateFormatted}`
        } 
        else {
          this.time = "Unknown city or timezone";
        }
      },


    dweather(data) {
      this.mindate= data.days[1].datetime;
      this.maxdate = data.days[14].datetime;
      this.forhourdata= data.days;
      this.daily=[];
      for (let i = 1; i <= 14; i++) {
        let hkun;
        let iconify= this.geticon(data.days[i].icon)
        const datetime = data.days[i].datetime;
        const date = new Date(datetime);
        const options = { weekday: 'long', locale: 'uz' };
        const dayOfWeek = date.toLocaleDateString('uz-UZ', options);
        if(i==1){
          hkun="Ertaga";
        }
        else{
          hkun=this.hkuns(dayOfWeek)
        }
        const sa=this.monthdate(data.days[i].datetime)
        let dtemp=this.selsiy(data.days[i].temp)

        let dinf = {
          wtemp: dtemp,
          wicon: iconify,
          weekday: hkun,
          tablesana: sa,
          wind: data.days[i].windspeed,

        }
        this.daily.push(dinf)
      }
    },
    async getdata() {
      try {
        let cname=""
        if(this.cityname==""){
          cname="Tashkent"
        }
        else{
          cname=this.cityname;
        }
        
        const response = await fetch(`https://weather.visualcrossing.com/VisualCrossingWebServices/rest/services/timeline/${cname}?unitGroup=us&key=S8GRPS82BJYVQWBERVPLSHJRA&contentType=json`)
        const data = await response.json();
        
        let iconify = this.geticon(data.currentConditions.icon);

        const timeString = data.currentConditions.sunrise;
        const [hours, minutes] = timeString.split(":");
        const qchiqv= `${hours}:${minutes}`;

        const another = data.currentConditions.sunset;
        const [hou, minu] = another.split(":");
        const qbotv = `${hou}:${minu}`
        this.forhourdata=data;
        let selsiytemp=this.selsiy(data.currentConditions.temp);
        let inf = {
          
          currentw: selsiytemp, 
          capname: data.resolvedAddress,
          windspeed: data.currentConditions.windspeed,
          foricon: iconify,
          qchiq: qchiqv,
          qbot: qbotv,
          humidity: data.currentConditions.humidity,

        }
        this.getTime(data.timezone);
        setInterval(()=>{
          this.getTime(data.timezone)
          },30000)
        
        this.dweather(data)
        this.data = inf;
      } catch (error) {
        console.error("City name is not found");
      }
    }
  }
}
</script>

<style scoped>

.hour{
    font-size: 50px;
    font-weight: 900;
}
.wd{
    font-size: 28px;
    font-weight: 700;
}
.shahar{
    font-size: 24px;
    font-weight: 600;
}
input:focus{
    outline: none;
}
input{
    outline: none;
    border: none;
    padding-left: 12px;
    width: 260px;
    height: 30px;
    font-size: 25px;
    background: rgba(148, 145, 145, 0.7);
    border-radius: 12px;
}

.container1{
    width: 86%;
    margin: auto;
    height: auto;
    display: flex;
    justify-content: space-between;
    flex-wrap: wrap;
    margin: 0 auto 8%;
}
#btn{
    border: none;
    background: transparent;
    margin-left: 3px;
    transform: translateY(12%);
    padding:5px 18px 0;
    border-radius: 12px;
    background-color: rgba(121, 117, 117, 0.7);
}
#btn> img{
    width: 25px;
}
.cont{
    display: flex;
    width: 100%;
    height: 20px;
    justify-content: space-between;
    align-items: center;
}
.cont p{
    display: inline;
    font-size: 16px;
}
.current{
    width: 80%;
    padding: 15px;
    margin-top: 20px;
    background-color: rgba(148, 145, 145, 0.7);
    border-radius: 20px;
}
.cnamedisplay, .timedisplay{
    margin-top: 8%;
}
.timedisplay{
    width: 300px;
    padding: 20px;
    height: 160px;
    border-radius: 15px;
}
img{
    width: 40px;
}

table{
    border-collapse: collapse;
    width: 100%;
    background-color: rgba(148, 145, 145, 0.7);
}
table, th, td{
    
    border: 2px solid black;
}
th{
    height: 50px;
}
td{
    text-align: center;
    width: 3%;
}
.second{
    transform: translateY(-100%);
    font-size: .7em;
    font-weight: 500;
}
.oxirgi{
    width: 86%;
    margin: auto;
    display: flex;
    flex-wrap: wrap;
    height: auto;
    margin: 40px auto;
    align-items: center;
    justify-content: space-between;
}
.inputcontainer{
    order: 2;
    width: 50%;
    display: flex;
    align-items: center;
    justify-content: space-between;
    flex-wrap: wrap;
}
.itis{
    width: 30%;
    order: 1;
}
.hourdata{
    width: 86%;
    margin: auto;
}
.yana{
    text-align: center;
}
@media(max-width: 805px){
  .container1{
    justify-content: center;
  }
}
@media(max-width: 1090px){
  .inputcontainer{
    order: 1;
    width: 100%;
  }
  .itis{
    order: 2;
    width: 100%;
    text-align: center;
    transform: translateY(-10%)
  }
}


</style>