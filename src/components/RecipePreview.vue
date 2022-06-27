<template>
  <span class="recipe-preview">
    <router-link :to="{ name: 'recipe', params: { recipeId: recipe.id } }"
    >
    <div class="recipe-body">
      <img v-b-tooltip.hover title="Click for full details" v-if="image_load" :src="recipe.image" class="recipe-image" />
    </div>
    </router-link>
    <div class="recipe-footer">
      <span v-if="viewed">
        <div :title="recipe.title" class="blue-recipe-title">
          {{ recipe.title }}
        </div>
      </span>
      <span v-else>  
        <div :title="recipe.title" class="recipe-title">
          {{ recipe.title }}
          </div>
      </span>
      <ul class="recipe-overview">
        <li>{{ recipe.readyInMinutes }} minutes</li>
        <li>{{ recipe.popularity }} likes</li>
      </ul>
      <img v-if="recipe.vegan" v-bind:src="vegen" class="recipe-props" />
      <img v-if="recipe.vegetarian" v-bind:src="vegetarian" class="recipe-props" />
      <img v-if="recipe.glutenFree" v-bind:src="glutenFree" class="recipe-props" />
      <img v-if="in_fav" v-bind:src="favy" class="recipe-props" />
      <img v-if="!in_fav" v-bind:src="favn" class="recipe-props" />

    </div>
  </span>
</template>

<script>
export default {
  mounted() {
    this.axios.get(this.recipe.image).then((i) => {
      this.image_load = true;
    });
    const response_fav = this.axios.get(
          "http://localhost:3000/user/favorites"
          //this.$root.store.server_domain + "/auth/Register",
    );
    response_fav.map((fav)=>{
      if(fav.id == this.recipe.id){
        this.in_fav = true;
      }
    });
    const response_view = this.axios.get(
          "http://localhost:3000/user/viewed"
          //this.$root.store.server_domain + "/auth/Register",
    );
    response_view.map((view)=>{
      if(view.id == this.recipe.id){
        this.viewed = true;
      }
    });


    
  },
  data() {
    return {
      image_load: false,
      in_fav: false,
      viewed: false, 
      vegen: require('../assets/vegan.png'),
      vegetarian: require('../assets/vegetarian.png'),
      glutenFree: require('../assets/gluten-free.png') ,
      favy: require('../assets/yes-fav.png') ,
      favn: require('../assets/no-fav.png')
    };
  },
  props: {
    recipe: {
      type: Object,
      required: true
    }

  }
};
</script>

<style scoped>
.recipe-preview {
  display: inline-block;
  width: 90%;
  height: 100%;
  position: relative;
  margin: 10px 10px;
}
.recipe-preview > .recipe-body {
  width: 100%;
  height: 200px;
  position: relative;
}

.recipe-preview .recipe-body .recipe-image {
  margin-left: auto;
  margin-right: auto;
  margin-top: auto;
  margin-bottom: auto;
  display: block;
  width: 98%;
  height: auto;
  -webkit-background-size: cover;
  -moz-background-size: cover;
  background-size: cover;
}

.recipe-preview .recipe-footer {
  width: 100%;
  height: 50%;
  overflow: hidden;
}

.recipe-preview .recipe-footer .recipe-title {
  padding: 10px 10px;
  width: 100%;
  font-size: 12pt;
  text-align: left;
  white-space: nowrap;
  overflow: hidden;
  -o-text-overflow: ellipsis;
  text-overflow: ellipsis;
}
.blue-recipe-title{
  padding: 10px 10px;
  color: blue;
  width: 100%;
  font-size: 12pt;
  text-align: left;
  white-space: nowrap;
  overflow: hidden;
  -o-text-overflow: ellipsis;
  text-overflow: ellipsis;
}

.recipe-preview .recipe-footer ul.recipe-overview {
  padding: 5px 10px;
  width: 100%;
  display: -webkit-box;
  display: -moz-box;
  display: -webkit-flex;
  display: -ms-flexbox;
  display: flex;
  -webkit-box-flex: 1;
  -moz-box-flex: 1;
  -o-box-flex: 1;
  box-flex: 1;
  -webkit-flex: 1 auto;
  -ms-flex: 1 auto;
  flex: 1 auto;
  table-layout: fixed;
  margin-bottom: 0px;
}
.recipe-props{
  height: 35px;  
  border-radius: 50%;
  width: 35px;
  text-align: center;
  margin-left: 5px;
}
.recipe-preview .recipe-footer ul.recipe-overview li {
  -webkit-box-flex: 1;
  -moz-box-flex: 1;
  -o-box-flex: 1;
  -ms-box-flex: 1;
  box-flex: 1;
  -webkit-flex-grow: 1;
  flex-grow: 1;
  width: 90px;
  display: table-cell;
  text-align: center;
}
</style>
