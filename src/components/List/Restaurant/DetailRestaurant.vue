<template>
    <div class="detailRestaurant container-flex">
        <ul>
            <li class="card" v-for="(data, index) in placeReviews" :key="index">
                <div class="row">
                    <div class="col-lg-1 col-md-1 pp p-0">
                        <img class="p-2" :src=data.profile_photo_url>
                    </div>
                    <div class="col-lg-3 col-md-10 p-0">
                        <h3>{{ data.author_name }}</h3>
                    </div>
                    <div class="col-lg-4 col-md-6">
                        <StarRating :value=data.rating :is-static="true" :active-color=starStyle.activeColor :color=starStyle.color></StarRating>
                    </div>
                    <div class="col-lg-4 col-md-6">
                        <p class="text-right p-3">{{ data.relative_time_description}}</p>
                    </div>
                </div>
                <p class="text" v-if="data.text">{{ data.text }}</p>
            </li>
            <!-- Commentaires personnalisées  -->
            <li class="card" v-for="(data, index) in allComment" :key="index">
                <div class="row">
                    <div class="col-lg-1 col-md-1 pp p-0"></div>
                    <div class="col-lg-3 col-md-10 p-0">
                        <h3>{{ data.name }}</h3>
                    </div>

                    <div class="col-lg-4 col-md-6">
                        <StarRating :value=data.rating :is-static="true" :active-color=starStyle.activeColor :color=starStyle.color></StarRating>
                    </div>
                    <div class="col-lg-4 col-md-6">
                        <p class="text-right p-3">{{ data.time}}</p>
                    </div>
                </div>
                <p class="text" v-if="data.text">{{ data.text }}</p>
            </li>
            <!-- Ajout de commentaire -->
            <li>
                <div v-on:click="newComment = true" v-if="!newComment" class="card" v-on:mouseover="mouseover" v-on:mouseleave="mouseleave" :class="mouseHoover">
                    <svg class="add-restaurant" xmlns="http://www.w3.org/2000/svg" fill="white" width="35" height="35" viewBox="0 0 24 24"><path d="M12 2c5.514 0 10 4.486 10 10s-4.486 10-10 10-10-4.486-10-10 4.486-10 10-10zm0-2c-6.627 0-12 5.373-12 12s5.373 12 12 12 12-5.373 12-12-5.373-12-12-12zm6 13h-5v5h-2v-5h-5v-2h5v-5h2v5h5v2z"/></svg>
                </div>
                <div v-if="newComment && !addComment" class="card new-restaurant">
                    <form>
                        <h1 class="pr-5 pl-5">Ajouter un commentaire</h1>
                        <svg v-on:click="newComment = false" class="cross" fill="white" xmlns="http://www.w3.org/2000/svg" width="25" height="25" viewBox="0 0 24 24"><path d="M24 20.188l-8.315-8.209 8.2-8.282-3.697-3.697-8.212 8.318-8.31-8.203-3.666 3.666 8.321 8.24-8.206 8.313 3.666 3.666 8.237-8.318 8.285 8.203z"/></svg>
                        <div class="from-group">
                            <label class="mt-3" for="name">Votre nom</label>
                            <input v-model="formData.name" type="text" id="nom" class="form-control">
                        </div>
                        <div class="from-group">
                            <label class="mb-2 mt-4" for="address">Votre commentaire</label>
                            <input v-model="formData.text" type="text" id="nom" class="form-control">
                        </div>
                        <div class="from-group mb-0 mt-3">
                            <label for="rate">Votre note</label>
                            <star-rating v-model="formData.rating" class="mr-4 mb-3 mt-0" :active-color=starStyle.activeColor :color=starStyle.color></star-rating>
                        </div>
                        <button v-on:click="createdComment" type="button" class="btn btn-outline-light">Ajouter</button>
                    </form>
                </div>
            </li>
        </ul>
    </div>
</template>
<script>
import axios from 'axios'
import StarRating from 'shapla-star-rating'

export default {
    name: 'DetailRestaurant',
    data() {
        return {
            placeReviews : null,
            allDetail : null,
            apiKey : 'AIzaSyBBgoHlkn7Z5HccxXMmARhJNOrPfx8QZlg',
            mouseHoover : null,
            starStyle : {
                activeColor: '#ed8a19',
                color: '#737373',
            },
            allComment : [],
            newComment : false,
            addComment : false,
            formData : {
                name: '',
                text: '',
                rating: 5,
                time: 'Il y a quelques secondes'
            },
        }
    },
    props: ['placeId'],
    components: {
        StarRating
    },
    mounted(){
        this.apiDetailCall()
    },
    methods : {
        apiDetailCall : function(){
             axios
            .get('https://cors-anywhere.herokuapp.com/https://maps.googleapis.com/maps/api/place/details/json?key='+ this.apiKey +'&place_id='+this.placeId)
            .then(reponse =>{
                this.allDetail = reponse.data
                this.placeReviews = reponse.data.result.reviews
            })   
        },
        createdComment : function(){
            this.newComment = false
            this.addComment = false
            this.allComment.push(this.formData)
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
.detailRestaurant{
    text-align: center;
}
.card{
    background-color: rgba(0, 0, 0, 0.4);
    border: 1px solid beige;
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
ul{
    height: 100%;
    padding: 0;
}
li{
    list-style-type: none ;
    color: beige;
}
img{
    width: 50px;
    height: 50px;
    display: inline;
} 
.pp{
    width: 4.5vw;
}
.text{
    margin-left: 4.5vw;
}
.star-rating{
	margin: 8px 0 0 30px ;
}
.new-restaurant{
    padding: 20px 0 30px 0;
}
.add-restaurant{
    margin: auto;
    margin-top: 15px;
    margin-bottom: 15px;
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
.form-control{
    background-color: beige ;
    width: 50%;
    margin: auto;
}
</style>