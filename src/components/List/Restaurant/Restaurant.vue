<template>
    <div class="restaurant" > 
        <div class="card" v-on:click="emitImgUrl()" v-on:mouseover="mouseover" v-on:mouseleave="mouseleave" :class="mouseHoover">  
            <h3>{{ restaurant.name }}</h3>
            <p>{{ restaurant.vicinity }}</p>
            <star-rating v-if="restaurant.rating != undefined" :value=parseInt(restaurant.rating) :is-static="true" :active-color=starStyle.activeColor :color=starStyle.color></star-rating>
        </div>  
        <transition name="fade">
            <DetailRestaurant v-show="show" :placeId=restaurant.place_id></DetailRestaurant>
        </transition>
    </div>
</template>
<script>
import DetailRestaurant from './DetailRestaurant'
import StarRating from 'shapla-star-rating'
import {bus} from '../../../main'

export default {
    name: 'Restaurant',
    data() {
        return {
            show: false,
            mouseHoover : null,
            starStyle : {
                activeColor: '#ed8a19',
                color: '#737373',
            },
            streetViewUrl : 'https://maps.googleapis.com/maps/api/streetview?size=1500x400&location='+ this.restaurant.geometry.location.lat +','+ this.restaurant.geometry.location.lng +'&fov=80&heading=70&pitch=0&key=AIzaSyBBgoHlkn7Z5HccxXMmARhJNOrPfx8QZlg'
        }
    },
    props: ['restaurant','real'],
    components: {
        StarRating,
        DetailRestaurant
    },
    methods: {
        emitImgUrl: function(){
            if(this.show == true){
                this.show = false
            }
            else{
                this.show = true
            }
            let streetViewObjet = {
                url: this.streetViewUrl,
                show: this.show
            }
            bus.$emit('streetViewObjet', streetViewObjet)
        },
        mouseover: function(){
            this.mouseHoover = "mouse-hoover"
        },
        mouseleave: function(){
            this.mouseHoover = null
        }
    }
}
</script>



<style scoped>
.card{
    background-color: rgba(255, 255, 255, 0);
    border: 1px solid beige;
    z-index: 1;
}
h3{
    padding: 15px 5px 5px 5px;
    margin: 0;
    font-size: 1.5rem;
    font-weight: bold;
    color: white;
}
p{
    margin: 0;
    padding-bottom: 5px;
    font-size: 0.90rem;
}
.star-rating{
    margin: auto;
    margin-bottom: 15px;
}

.mouse-hoover{
    background-color:  rgba(255, 123, 0, 0.3);
}
.fade-enter-active {
    transition: all 500ms ease;
}
.fade-leave-active {
    transition: all 50ms ease;
}
.fade-enter, .fade-leave-to /* .fade-leave-active below version 2.1.8 */ {
    transform: scaleY(-1000px);
    overflow: hidden;
    opacity: 0;
}
.image-restaurant{
    position: absolute;
    z-index: 1;
    left: 50vw;
    bottom: 0;
    width: 50vw;
}
img {
  width: 100%;
  height: auto;
}
</style>