<template>
  <div class="container">
    <div v-if="recipe">
      <div class="recipe-header mt-3 mb-4">
        <h1> <b>{{ recipe.title }}</b></h1>
        <img :src="recipe.image" class="center" />
      </div>
      <div class="recipe-body">
        <div class="wrapper">
          <div class="wrapped">
          <b-tabs pills card>

            <b-tab title="General Information" active><b-card-text>
            <div>Ready in {{ recipe.readyInMinutes }} minutes</div>
              <div>Vegetarian: {{ recipe.vegetarian }} </div>
              <div>Vegan: {{ recipe.vegan }} </div>
              <div>Gluten Free: {{ recipe.glutenFree }} </div>
              <div v-if="isNaN(recipe.id)">Servings: {{ recipe.servings }}  </div>
              <div v-if="isNaN(recipe.id)">Price per serving: {{ recipe.pricePerServing }} $  </div>
              <div v-if="isNaN(recipe.id)">Total likes: {{ recipe.aggregateLikes }}  </div>

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
                  <b-form-checkbox v-on:change="increment($event)">
                  {{ s.step }}
                </b-form-checkbox>
                </li>
              </ol>
              <round-slider
                class="slider"
                v-model= value
                start-angle="315"
                end-angle="+270"
                line-cap="round"
                radius="120"
                readOnly="true"
                />
              </b-card-text></b-tab>
      </b-tabs>
        </div>
      </div>
    </div>
      </div>
          </div>

  </template>

  <script>
import RoundSlider from 'vue-round-slider'
export default {
  components: {
    RoundSlider,
  },
  data() {
    return {
      recipe: null,
      count: 0,
      value:0
    };
  },
  async created() {
    try {
      let response;
      try {
        response = await this.axios.get(
          "http://localhost:3000/recipes/"+this.$route.params.recipeId,
        );

        if (response.status !== 200) this.$router.replace("/NotFound");
      } catch (error) {
        console.log("error.response.status", error.response.status);
        this.$router.replace("/NotFound");
        return;
      }
      let {
        analyzedInstructions,
        instructions,
        extendedIngredients,
        aggregateLikes,
        readyInMinutes,
        image,
        title,
        vegetarian,
        vegan,
        glutenFree,
        pricePerServing,
        servings,
        healthScore
      } = response.data;

      var _instructions="";
      if(isNaN(this.$route.params.recipeId)){
        _instructions = analyzedInstructions;
      }
      else{
        _instructions = analyzedInstructions
        .map((fstep) => {
          fstep.steps[0].step = fstep.name + fstep.steps[0].step;
          return fstep.steps;
        })
        .reduce((a, b) => [...a, ...b], []);
      }

      let _recipe = {
        instructions,
        _instructions,
        analyzedInstructions,
        extendedIngredients,
        aggregateLikes,
        readyInMinutes,
        image,
        title,
        vegetarian,
        vegan,
        glutenFree,
        pricePerServing,
        servings,
        healthScore
      };

      this.recipe = _recipe;
      if(this.$route.params.recipeId){
        this.recipe.vegan = this.recipe.vegan == '0' ? "false" : "true";
        this.recipe.vegetarian = this.recipe.vegetarian == '0' ? "false" : "true";
        this.recipe.glutenFree = this.recipe.glutenFree == '0' ? "false" : "true";
      }
    } catch (error) {
      console.log(error);
      this.$root.toast("OOPS", "We were unable to fully load the page, please try again", "danger");

    }
  },
  methods:{
    increment(event){
        const isChecked = event;
        if(isChecked){
            this.count++;
        }else{
            this.count--;
        } 
        this.value = (this.count/this.recipe._instructions.length)*100

    }
  }
};
</script>

<style scoped>
.slider{
  align:center;
  margin:auto;
}
.wrapped {
  width: 70%;
  align:center;
  margin:auto;
}
.center {
  display: block;
  margin-left: auto;
  margin-right: auto;
  width: 70%;
}
/* .recipe-header{

} */
h1{
  text-align:center;
  margin-bottom:20px;
  font-family: "Times New Roman", Times, serif;
  
}
img{
  margin-bottom:20px;
}
.recipe-body{
  margin:auto;
  align:center;
}
.container{
  align:center;
}
</style>
