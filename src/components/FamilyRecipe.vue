<template>
  <div class="container">
    <div v-if="recipe">
      <div class="recipe-header mt-3 mb-4">
        <h1>{{ recipe.title }}</h1>
        <h4>Owner Recipe: {{recipe.ownerRecipe}}</h4>
        <img :src="recipe.image" class="center" />
      </div>
      <div class="recipe-body">
        <div class="wrapper">
          <div class="wrapped">
          <b-tabs pills card>

            <b-tab title="General Information" active><b-card-text>
            <div>{{recipe.WhenDoWeEat}}</div>
            <div>Ready in {{ recipe.readyInMinutes }} minutes</div>
              <div>Likes: {{ recipe.aggregateLikes }} likes</div>
              <div>Vegetarian: {{ recipe.vegetarian }} </div>
              <div>Vegan: {{ recipe.vegan }} </div>
              <div>Gluten Free: {{ recipe.glutenFree }} </div>
              
            </b-card-text></b-tab>


            <b-tab title="Ingredients"><b-card-text>
              <ul>
                <li
                  v-for="(r, index) in recipe.extendedIngredients"
                  :key="index + '_' + r.id"
                >
                  {{ r.original }}
                </li>
              </ul>
            </b-card-text></b-tab>
            <b-tab title="Instructions"><b-card-text>
              <ol>
                <li v-for="s in recipe._instructions" :key="s.number">
                  <b-form-checkbox>
                  {{ s.step }}
                </b-form-checkbox>
                </li>
              </ol>
            </b-card-text></b-tab>
    </b-tabs>
      </div>
    </div>
  </div>
    </div>
        </div>


</template>

<script>
export default {
    props: {
    recipe: {
      type: Object,
      required: true
    }
  },
  async mounted() {
    console.log(this.recipe);
    // this.axios.get(this.recipe.image).then((i) => {
    //   this.image_load = true;
    // });    
  },
    data() {
        return {
            image_load: false,
            vegen: require('../assets/vegan.png'),
            vegetarian: require('../assets/vegetarian.png'),
            glutenFree: require('../assets/gluten-free.png'),
        }
    },


}
</script>

<style>

</style>