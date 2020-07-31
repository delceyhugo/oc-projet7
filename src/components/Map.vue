<template>
    <div class="map ">
        <div id="map"></div>
    </div>
</template>
<script>
import {bus} from '../main'
    export default {
        name : 'Map',
        data(){
            return{
                map : null,
                sortedRestaurant : null,
                injectScriptStatut : false,
            }
        },
        props:['apiKey','coordsPosition','lat','lng'],
        mounted() {
            if(this.injectScriptStatut == false){
                this.injectScript()
            }
            // Récup du tableaux des restaurants triés
            bus.$on('sortedRestaurant', (data) => {
                this.sortedRestaurant = data
                this.newMap()
            })
        },
        methods: {
            injectScript: function(){
                // Injection du script GoogleMap
                const scriptGM = document.createElement('script')
                scriptGM.src = 'https://maps.googleapis.com/maps/api/js?key='+this.apiKey
                scriptGM.defer = true
                scriptGM.async = true
                document.head.appendChild(scriptGM)
                this.injectScriptStatut = true

            },
            newMap: function(){
                // Création d'une map
                if(!this.coordsPosition){
                    return
                }
                this.map = new window.google.maps.Map(document.getElementById("map"), {
                    center: {
                        lat: this.lat,
                        lng: this.lng
                    },
                    zoom: 13,
                    styles: [
                                { elementType: "geometry", stylers: [{ color: "#242f3e" }] },
                                { elementType: "labels.text.stroke", stylers: [{ color: "#242f3e" }] },
                                { elementType: "labels.text.fill", stylers: [{ color: "#746855" }] },
                                {
                                    featureType: "administrative.locality",
                                    elementType: "labels.text.fill",
                                    stylers: [{ color: "#d59563" }]
                                },
                                {
                                    featureType: "poi",
                                    elementType: "labels.text.fill",
                                    stylers: [{ color: "#d59563" }]
                                },
                                {
                                    featureType: "poi.park",
                                    elementType: "geometry",
                                    stylers: [{ color: "#263c3f" }]
                                },
                                {
                                    featureType: "poi.park",
                                    elementType: "labels.text.fill",
                                    stylers: [{ color: "#6b9a76" }]
                                },
                                {
                                    featureType: "road",
                                    elementType: "geometry",
                                    stylers: [{ color: "#38414e" }]
                                },
                                {
                                    featureType: "road",
                                    elementType: "geometry.stroke",
                                    stylers: [{ color: "#212a37" }]
                                },
                                {
                                    featureType: "road",
                                    elementType: "labels.text.fill",
                                    stylers: [{ color: "#9ca5b3" }]
                                },
                                {
                                    featureType: "road.highway",
                                    elementType: "geometry",
                                    stylers: [{ color: "#746855" }]
                                },
                                {
                                    featureType: "road.highway",
                                    elementType: "geometry.stroke",
                                    stylers: [{ color: "#1f2835" }]
                                },
                                {
                                    featureType: "road.highway",
                                    elementType: "labels.text.fill",
                                    stylers: [{ color: "#f3d19c" }]
                                },
                                {
                                    featureType: "transit",
                                    elementType: "geometry",
                                    stylers: [{ color: "#2f3948" }]
                                },
                                {
                                    featureType: "transit.station",
                                    elementType: "labels.text.fill",
                                    stylers: [{ color: "#d59563" }]
                                },
                                {
                                    featureType: "water",
                                    elementType: "geometry",
                                    stylers: [{ color: "#17263c" }]
                                },
                                {
                                    featureType: "water",
                                    elementType: "labels.text.fill",
                                    stylers: [{ color: "#515c6d" }]
                                },
                                {
                                    featureType: "water",
                                    elementType: "labels.text.stroke",
                                    stylers: [{ color: "#17263c" }]
                                }
                                ]
                })
                // Marker pour les restaurants triés
                this.sortedRestaurant.forEach((data) => {
                    this.createRestaurantMarker(data)
                })
                this.createClientMarker()
                // Configure the click listener.
                this.map.addListener('click', function(mapsMouseEvent) {
                    let str = mapsMouseEvent.latLng.toString()
                    str = str.replace('(','')
                    str = str.replace(')','')
                    let res = str.split(',');
                    let lat = parseFloat(res[0])
                    let lng = parseFloat(res[1])
                    let newRestaurantCoords = {
                        newRestaurantLat: lat,
                        newRestaurantLng: lng
                    }
                    let newRestaurantMarker = new window.google.maps.Marker({
                        position: new window.google.maps.LatLng(newRestaurantCoords.newRestaurantLat, newRestaurantCoords.newRestaurantLng),
                        map: this.map
                    })
                    bus.$emit('newRestaurantCoords', newRestaurantCoords)
                    return newRestaurantMarker
                })
                return this.map 
            },
            createRestaurantMarker: function(data){
                const lat = data.geometry.location.lat
                const lng = data.geometry.location.lng
                let marker = new window.google.maps.Marker({
                    position: new window.google.maps.LatLng(lat, lng),
                    map: this.map,
                    title: 'data.name'
                })
                let infowindow = new window.google.maps.InfoWindow()
                marker.addListener("click", () => {
                    infowindow.setContent(`<div class="ui header">${data.name}</div><p>${data.vicinity}</p>`)
                    infowindow.open(this.map, marker)
                }) 
                return marker
            },
            createClientMarker: function(){
                const lat = this.lat
                const lng = this.lng
                let marker = new window.google.maps.Marker({
                    position: new window.google.maps.LatLng(lat, lng),
                    map: this.map,
                    icon: {
                        url: "https://img.icons8.com/plasticine/2x/user-location.png",
                        scaledSize: new window.google.maps.Size(50, 50)
                    }
                })
                let infowindow = new window.google.maps.InfoWindow()
                marker.addListener("click", () => {
                    infowindow.setContent(`<div class="ui header pr-3 pb-2">Votre position</div>`)
                    infowindow.open(this.map, marker)
                }) 
                return marker
            }
        }
    }
</script>
<style>
#map {
    height: 100vh;
    color: beige;
}
.gm-style, .gm-style-iw-d {
    overflow: hidden !important;
    font-size: 10px;
}
.gm-style, .gm-style-iw-c {
    background-color: gray !important;
}
.ui{
    font-size: 15px;
}
.gm-style-iw-d p {
    font-size: 12px;
}



</style>
    