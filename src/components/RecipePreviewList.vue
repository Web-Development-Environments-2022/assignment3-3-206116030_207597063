<template>
  <b-container>
    <h3 class="title-1">
      {{ title }}:
      <slot></slot>
    </h3>
    <b-row>
      <ul v-if="title=='Search result'">
        <b-row v-for="r in recipes" :key="r.id">
          <RecipePreview class="recipePreview" :recipe="r" />
        </b-row>
      </ul>
      <ul v-else-if="title=='Family recipes'">
        <b-row v-for="r in recipes" :key="r.id">
          <FamilyRecipe class="FamilyRecipe" :recipe="r"/>
        </b-row>
      </ul>
      <ul v-else>
        <b-col v-for="r in recipes" :key="r.id">
          <RecipePreview class="recipePreview" :recipe="r" />
        </b-col>
      </ul>
    </b-row>
    <p v-if="error" >No results have been found!</p>
  </b-container>
</template>

<script>
import RecipePreview from "./RecipePreview.vue";
import FamilyRecipe from "./FamilyRecipe.vue";
export default {
  name: "RecipePreviewList",
  components: {
    RecipePreview,
    FamilyRecipe
  },
  props: {
    title: {
      type: String,
      required: true
    },
    path: {
      type: String,
      required: true
    }
  },
  data() {
    return {
      recipes: [],
      error: false
    };
  },
  mounted() {
    this.updateRecipes();
  },
  methods: {
    async updateRecipes() {
      try {
        console.log(this.path);
        const response = await this.axios.get(
          "http://localhost:3000" + this.path,
        );
        console.log(response);
        this.error = false;
        const recipes = response.data;
        console.log(recipes)
        this.recipes = [];
        this.recipes.push(...recipes);
        if(this.recipes.length == 0){
          this.error = true;
        }
      } catch (error) {
        console.log(error);
      }
    }
  }
};
</script>

<style lang="scss" scoped>
.container {
  min-height: 400px;
}
.title-1{
  font-family: "Times New Roman", Times, serif;
}
</style>
