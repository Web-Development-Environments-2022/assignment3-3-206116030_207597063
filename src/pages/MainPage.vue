<template>
  <div class="container">
    <h1 class="title">Main Page</h1>
    <RecipePreviewList :key="key" title="Random Recipes" path="/recipes/random" class="RandomRecipes center" />
    <button v-on:click="random">Random</button>
    <router-link v-if="!$root.store.username" to="/login" tag="button">You need to Login to vue this</router-link>
    <span v-if="$root.store.username">
      <RecipePreviewList title="Last Viewed" path="/user/viewed3" class="RandomRecipes center"/>
    </span>
  </div>
</template>

<script>
import RecipePreviewList from "../components/RecipePreviewList";
export default {
  components: {
    RecipePreviewList
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
.RandomRecipes {
  margin: 10px 0 10px;
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
