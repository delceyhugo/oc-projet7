<template>
    <div v-if="apiLoad" class="list">
        <div class="scrollbar" id="style-1">
            <ul>
                <li>
                    <div v-on:click="newRestaurant = true" v-if="!newRestaurant" class="card" v-on:mouseover="mouseover" v-on:mouseleave="mouseleave" :class="mouseHoover">
                        <svg class="add-restaurant" xmlns="http://www.w3.org/2000/svg" fill="white" width="35" height="35" viewBox="0 0 24 24"><path d="M12 2c5.514 0 10 4.486 10 10s-4.486 10-10 10-10-4.486-10-10 4.486-10 10-10zm0-2c-6.627 0-12 5.373-12 12s5.373 12 12 12 12-5.373 12-12-5.373-12-12-12zm6 13h-5v5h-2v-5h-5v-2h5v-5h2v5h5v2z"/></svg>
                    </div>
                    <div v-if="newRestaurant && !addRestaurant" class="card new-restaurant">
                        <form>
                            <h1 class="pr-5 pl-5">Ajouter un nouveau restaurant</h1>
                            <svg v-on:click="newRestaurant = false" class="cross" fill="white" xmlns="http://www.w3.org/2000/svg" width="25" height="25" viewBox="0 0 24 24"><path d="M24 20.188l-8.315-8.209 8.2-8.282-3.697-3.697-8.212 8.318-8.31-8.203-3.666 3.666 8.321 8.24-8.206 8.313 3.666 3.666 8.237-8.318 8.285 8.203z"/></svg>
                            <div class="from-group">
                                <label for="name">Nom du restaurant</label>
                                <input v-model="formData.name" type="text" id="nom" class="form-control">
                            </div>
                            <div class="from-group">
                                <label for="name">Adresse du restaurant</label>
                                <input v-model="formData.vicinity" type="text" id="nom" class="form-control">
                            </div>
                            <div class="from-group">
                                <label class="mb-0" for="address">Adresse du restaurant</label>
                                <p class="m0 p0"><small><em>Clicker sur la map pour ajouter les coordonnées du restaurant</em></small></p>
                                <p id="adresse" class="form-control">{{ formData.coords }}</p>
                            </div>
                            <div class="from-group">
                                <label for="rate">Note du restaurant</label>
                                <star-rating v-model="formData.rating" :active-color=starStyle.activeColor :color=starStyle.color></star-rating>
                            </div>
                            <button v-on:click="createdRestaurant" type="button" class="btn btn-outline-light">Ajouter</button>
                        </form>
                    </div>
                </li>
                <li v-for="(data, index) in sortedRestaurant" :key="index">
                    <restaurant :restaurant=data :real=data.real></restaurant>
                </li>
            </ul>
        </div>
    </div>
</template>
<script>
import axios from 'axios'
import {bus} from '../../main'
import Restaurant from './Restaurant/Restaurant'
import StarRating from 'shapla-star-rating'

export default {
    name: 'List',
    data(){
        return{
            apiLoad : false,
            allRestaurant : null,
            sortedRestaurant : [],
            addedRestaurant : [],
            newRestaurant : false,
            addRestaurant : false,
            formData : {
                name: '',
                vicinity: '',
                rating: 5,
                coords: '',
                geometry : {
                    location : {
                        lat : '',
                        lng :''
                    }
                }
            },
            mouseHoover : null,
            lowVal : 0,
            topVal : 5,
            starStyle : {
                activeColor: '#ed8a19',
                color: '#737373',
            }
        }
    },
    mounted(){
        bus.$on('topVal', (data) => {
            this.topVal = data
            this.filterByStar()
        })
        bus.$on('lowVal', (data) => {
            this.lowVal = data
            this.filterByStar()
        })
        bus.$on('newRestaurantCoords', (data) => {
            if(this.newRestaurant == false){
                this.newRestaurant = true
            }
            console.log(data.newRestaurantLat.toFixed(5))
            let coords = 'Lat : ' + data.newRestaurantLat.toFixed(5) + ', Lng : ' + data.newRestaurantLng.toFixed(5)
            this.formData.coords = coords
            this.formData.geometry.location.lat = data.newRestaurantLat
            this.formData.geometry.location.lng = data.newRestaurantLng
        })
    },
    components: {
        Restaurant,
        StarRating
    },
    props: ['apiKey','coordsPosition'],
    watch: {
        coordsPosition : function(){
            this.apiCall()
        },
    },
    methods: {
        apiCall : function(){
            // Get la liste des restaurants a proximité
            axios
            .get('https://cors-anywhere.herokuapp.com/https://maps.googleapis.com/maps/api/place/nearbysearch/json?key='+ this.apiKey +'&location='+ this.coordsPosition +'&radius=2500&type=restaurant')
            .then(reponse =>{
                this.test = reponse
                this.allRestaurant = reponse.data.results
                this.apiLoad = true
                this.filterByStar()
            })   
        },
        filterByStar : function(){
            // Trier les restaurants par note
            this.sortedRestaurant = []
            this.allRestaurant.forEach(data => {
                if( this.lowVal <= data.rating && Math.ceil(data.rating) <= this.topVal){
                    this.sortedRestaurant.push(data)
                }
            })
            bus.$emit('sortedRestaurant', this.sortedRestaurant)
        },
        createdRestaurant : function(){
            this.newRestaurant = false
            this.addRestaurant = false
            this.allRestaurant.push(this.formData)
            this.filterByStar()
            this.formData = {}
        },
        mouseover: function(){
            this.mouseHoover = "mouse-hoover"
        },
        mouseleave: function(){
            this.mouseHoover = null
        },
    }
}
</script>
<style scoped>
.list{
    height: 80vh;
}
ul{
    height: 100%;
    padding: 0;
}
li{
    list-style-type: none ;
    color: beige;
}
h1{
    color: white;
}
.card{
    background-color: rgba(255, 255, 255, 0);
    border: 1px solid beige;
    z-index: 1;
}
.new-restaurant{
    padding: 20px 0 30px 0;
}
.add-restaurant{
    margin: auto;
    margin-top: 15px;
    margin-bottom: 15px;
}
.scrollbar
{
    float: left;
    height: 100%;
    width: 100%;
    overflow-y: scroll;
    margin-bottom: 25px;
}
label:first-child{
    margin-top: 15px;
}
.form-control{
    background-color: beige ;
    width: 50%;
    margin: auto;
}
#style-1::-webkit-scrollbar-track
{
    box-shadow: inset 0 0 6px rgba(0,0,0,0.3);
    border-radius: 10px;
    background-color: beige;
}

#style-1::-webkit-scrollbar
{
    width: 12px;
}

#style-1::-webkit-scrollbar-thumb
{
    border-radius: 10px;
    box-shadow: inset 0 0 6px rgba(0,0,0,.3);
    background-color: #555;
}
button{
    margin-top: 25px;
}
.cross{
    position: absolute;
    margin: 20px;
    top: 10px;
    right: 10px;
}
.mouse-hoover{
    background-color:  rgba(255, 123, 0, 0.3);
}
</style>