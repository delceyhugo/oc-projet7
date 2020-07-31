<template>
  <div id="app">
    <div class="container-fluid">
      <div class="row">
        <div class="col-lg-6 p-0">
          <div class="row">
            <top-header class="col-lg-12"></top-header>
          </div>
          <div class="row">
            <list class="col-12 p-0" :apiKey="apiKey" :coordsPosition='coordsPosition'></list>
          </div>
        </div>
        <div class="col-lg-6 p-0">
          <maps class="col-lg-12 p-0" :apiKey="apiKey" :coordsPosition='coordsPosition' :lat='lat' :lng='lng'></maps>
          <footer-img v-show="show" :imgSrc='streetViewurl'></footer-img>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import List from './components/List/List.vue'
import Maps from './components/Map.vue'
import Header from './components/Header.vue'
import FooterImg from './components/FooterImg.vue'
import {bus} from './main'

export default {
  name: 'App',
  data(){
    return{
      apiKey : 'AIzaSyBBgoHlkn7Z5HccxXMmARhJNOrPfx8QZlg',
      coordsPosition : null,
      lat : null,
      lng : null,
      show : false,
      streetViewurl : ''
    }
  },
  components: {
    'list' : List,
    'maps' : Maps,
    'top-header' : Header,
    'footer-img' : FooterImg
  },
  mounted() {
    navigator.geolocation.getCurrentPosition(this.setPosition)
    bus.$on('streetViewObjet', (data) => {
      this.show = data.show
      this.streetViewurl = data.url
    })
    
  },
  watch:{
    show: function(){
      if(this.show == true){
        document.getElementById('map').style.height = "70vh"
      }
      else if(this.show == false){
        document.getElementById('map').style.height = "100vh"
      }
    }
  },
  methods: {
    setPosition : function(position){
      this.coordsPosition = position.coords.latitude +','+position.coords.longitude, 
      this.lat = position.coords.latitude,
      this.lng = position.coords.longitude 
    }
  },
}
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Montserrat&family=Nunito:wght@300&display=swap');

h1,h2,h3{
  font-family: 'Montserrat', sans-serif !important;
}
p,span{
  font-family: 'Nunito', sans-serif !important;
}
h3{
    padding: 15px 5px 5px 5px;
    margin: 0;
    font-size: 1.2rem;
    color: white;
}
p{
    margin: 5px;
    padding-bottom: 5px;
    font-size: 0.90rem;
}
#app {
  scroll-behavior: smooth;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
  background: #323232;
  height: 100vh;
  width: 100vw;
  margin: 0px;
}
.container-fluid{
  padding: 0px !important;
}
.row{
  margin: 0px !important;
}
.col{
  padding: 0px !important;
}

</style>
