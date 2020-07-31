<template>
    <div class="header">
        <h1>Liste des restaurants</h1>
        <h2>↓ Trier par évaluation ↓</h2>
        <span class="multi-range">
            <input v-on:click="lowValue" type="range" min="0" max="5" value="0" id="lower">
            <input v-on:click="topValue" type="range" min="0" max="5" value="5" id="upper">
        </span>
        <p>Entre {{lowVal}} et {{topVal}} étoiles</p>
    </div>
</template>

<script>
import {bus} from '../main'
export default {
    name: 'Header',
    data(){
        return{
            lowVal : 0,
            topVal : 5,
            lowSlide : null,
            topSlide : null,
        }
    },
    mounted(){
            this.lowSlide = document.querySelector('#lower')
            this.topSlide = document.querySelector('#upper')
            this.topSlide.oninput = this.topValue()
            this.lowSlide.oninput = this.lowValue()
    },
    methods:{
        topValue : function() {
            this.lowVal = parseInt(this.lowSlide.value);
            this.topVal = parseInt(this.topSlide.value);
            if (this.topVal < this.lowVal + 1) {
                this.topSlide.value = this.lowVal + 1;
                if (this.lowVal == this.lowSlide.min) {
                    this.topSlide.value = 1;
                }
            }
            bus.$emit('topVal', this.topVal)
        },
        lowValue :  function() {
            this.lowVal = parseInt(this.lowSlide.value);
            this.topVal = parseInt(this.topSlide.value);
            if (this.lowVal > this.topVal - 1) {
                this.lowSlide.value = this.topVal - 1;
                if (this.topVal == this.topSlide.max) {
                    this.lowSlide.value = parseInt(this.topSlide.max) - 1;
                }
            }
            bus.$emit('lowVal', this.lowVal)
        }
    },
}
</script>




<style scoped>
.header{
    height: 20vh;
}
h1{
    color: white;
}
h2{
    margin-top: 25px;
    color: beige;
}
p{
    margin-top: 20px;
    color: beige;
}
input[type="range"] {
    box-sizing: border-box;
    appearance: none;
    width: 300px;
    background: linear-gradient(grey, grey) no-repeat center;
    background-size: 100% 4px;
    pointer-events: none;
    outline: none;
}
input[type="range"]::-webkit-slider-thumb {
    height: 28px;
    width: 28px;
    border-radius: 28px;
    background-color: #fff;
    cursor: pointer;
    appearance: none;
    pointer-events: all;
}
.multi-range{
    display: flex;
    margin: auto;
    align-items: center;
    justify-content: center; 
    flex-direction: column;
    margin: 20px 0px;
}
.multi-range input[type="range"] {
    position: absolute;
}
.multi-range input[type="range"]:nth-child(2) {
  background: none;
}
</style>