<template>
  <div class="container">
    <div class="title">
    <h1>COOK, EAT, SLEEP & REPEAT</h1>
    </div>
    <div class="middle">
    <div class="divon">
      <RecipePreviewList :key="key" title="Suprise Me" path="/recipes/random" class="RandomRecipes center" />
      <b-button squared variant="secondary" v-on:click="random">Show me more!</b-button>
    </div>  

    <div class="divon" v-if="$root.store.username">
        <RecipePreviewList title="Watch it again" path="/user/viewed3" class="RandomRecipes center"/>
    </div>

    <div class="divon" v-if="!$root.store.username">
      <LoginPage></LoginPage>
    </div>
  </div>
  </div>
</template>

<script>
import RecipePreviewList from "../components/RecipePreviewList";
import LoginPage from "../pages/LoginPage";

export default {
  components: {
    RecipePreviewList,
    LoginPage
  },
  data(){
    return{
      key: 0
    };
  },
  methods:{
    random: function() {
    this.key +=1;


  }
  },


};
</script>

<style lang="scss" scoped>
.container{
  text-align:center;
}
.title{
  margin-top:30px;  
  text-align: center;
}
.middle {
    width:100%;
    display: grid;
    grid-template-columns: 1fr 1fr;
    grid-gap: 100px;
}
.divon{
  width:100%;
}



.blur {
  -webkit-filter: blur(5px); /* Safari 6.0 - 9.0 */
  filter: blur(2px);
}
::v-deep .blur .recipe-preview {
  pointer-events: none;
  cursor: default;
}


</style>
