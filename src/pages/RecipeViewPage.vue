<template>
  <div class="container">
    <div v-if="recipe">
      <div class="recipe-header mt-3 mb-4">
        <h1>{{ recipe.title }}</h1>
        <img :src="recipe.image" class="center" />
      </div>
      <div class="recipe-body">
        <div class="wrapper">
          <div class="wrapped">
          <b-tabs pills card>

            <b-tab title="General Information" active><b-card-text>
            <div>Ready in {{ recipe.readyInMinutes }} minutes</div>
              <div>Likes: {{ recipe.aggregateLikes }} likes</div>
              <div>Vegetarian: {{ recipe.vegetarian }} </div>
              <div>Vegan: {{ recipe.vegan }} </div>
              <div>Gluten Free: {{ recipe.glutenFree }} </div>
              <div>Price per serving: {{ recipe.pricePerServing }}$</div>
              <div>Servings: {{ recipe.servings }} </div>
              <div>Health score: {{ recipe.healthScore }}/100  </div>
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
  data() {
    return {
      recipe: null
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
        serving,
        healthScore
      } = response.data;

      let _instructions = analyzedInstructions
        .map((fstep) => {
          fstep.steps[0].step = fstep.name + fstep.steps[0].step;
          return fstep.steps;
        })
        .reduce((a, b) => [...a, ...b], []);

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
        serving,
        healthScore
      };

      this.recipe = _recipe;
    } catch (error) {
      console.log(error);
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
