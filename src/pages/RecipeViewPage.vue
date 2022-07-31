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
            <div>Ready in {{ recipe.readyInMinutes }} minutes</div>
              <div>Vegetarian: {{ recipe.vegetarian }} </div>
              <div>Vegan: {{ recipe.vegan }} </div>
              <div>Gluten Free: {{ recipe.glutenFree }} </div>
              <div>{{recipe.WhenDoWeEat}}</div>
              <div v-if="!recipe.id.startsWith('d')">Health score: {{ recipe.healthScore }}/100  </div>
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
        console.log(this.$route.params.recipeId)
        response = await this.axios.get(
          "http://localhost:3000/recipes/"+this.$route.params.recipeId,
        );

        if (response.status !== 200) this.$router.replace("/NotFound");
      } catch (error) {
        console.log("error.response.status", error.response.status);
        this.$router.replace("/NotFound");
        return;
      }
      console.log(response);
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

      if(this.$route.params.recipeId.startsWith('d')){
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

.wrapped {
  width: 60%;
}
.center {
  display: block;
  margin-left: auto;
  margin-right: auto;
  width: 50%;
}
/* .recipe-header{

} */
h1{
  text-align:center;
  margin-bottom:20px;
}
img{
  margin-bottom:20px;
}
.recipe-body{
  margin:auto;
}
.container{
  align:center;
}
</style>
