<template>
    <div class="container">
        <RecipePreviewList :key="key" title="Our Family Recipes" path="/recipes/getfamilyRecipes" class="FamilyRecipes center" />

    </div>

</template>

<script>
import RecipePreviewList from "../components/RecipePreviewList";
export default {
    name: "OurFamilyRecipes",
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
.FamilyRecipes {
  margin: 10px 0 10px;
}
</style>