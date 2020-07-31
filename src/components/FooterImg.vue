<template>
    <div class="footer-img">
        <div class="row p-0">
            <img class="col-lg-6 col-md-12 p-0 mx-auto street-view" :src='imgSrc'>
        </div>
    </div>
</template>


<script>
export default {
    name: 'Comment',
    data() {
        return {
            
        }
    },
    props:['imgSrc'],


}
</script>
<style>
.street-view{
  height: 30vh;
  text-align: start !important;
  border: 1px solid beige;
}   
</style>