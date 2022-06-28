<template>
  <div class="container">
    <h1 class="title">Search Page</h1>
    <b-form @submit.prevent="onSearch" @reset.prevent="onReset">

      <b-form-group
      id="input-group-name"
      label-cols-sm="3"
      label="nameRecipe:"
      label-for="nameRecipe"
      >
        <b-form-input
            id="nameRecipe"
            v-model="form.nameRecipe"
            type="text"
            :state="validateState('nameRecipe')"
          ></b-form-input>
          <b-form-invalid-feedback v-if="!$v.form.nameRecipe.required">
            Recipe's name is required
          </b-form-invalid-feedback>
        </b-form-group>

        <b-form-group
      id="input-group-amount"
      label-cols-sm="3"
      label="amount:"
      label-for="amount"
      >
        <b-form-select
            id="amount"
            v-model="form.amount"
            :options="amount_options"
            :state="validateState('amount')"
          ></b-form-select>
          <b-form-invalid-feedback v-if="!$v.form.amount.required">
            amount is required
          </b-form-invalid-feedback>
        </b-form-group>

        <b-form-group
      id="input-group-sort"
      label-cols-sm="3"
      label="sort:"
      label-for="sort"
      >
        <b-form-select
            id="sort"
            v-model="form.sort"
            :options="sort_options"
            :state="validateState('sort')"
          ></b-form-select>
          <b-form-invalid-feedback v-if="!$v.form.sort.required">
            sort is required
          </b-form-invalid-feedback>
        </b-form-group>

      <b-form-group
      id="input-group-filter"
      label-cols-sm="3"
      label="filter:"
      label-for="filter"
      >
      <input class="form-check-input" v-model="form.filter" type="checkbox" id="filter"/>
      </b-form-group>

      <b-form-group
      id="input-group-cuisine"
      label-cols-sm="3"
      label="cuisine:"
      label-for="cuisine"
      >
        <b-form-select
            id="cuisine"
            v-model="form.cuisine"
            :disabled="!form.filter"
            :options="cuisines"
        ></b-form-select>
      </b-form-group>

      <b-form-group
      id="input-group-diet"
      label-cols-sm="3"
      label="diet:"
      label-for="diet"
      >
        <b-form-select
            id="diet"
            v-model="form.diet"
            :disabled="!form.filter"
            :options="diets"
        ></b-form-select>
      </b-form-group>

      <b-form-group
      id="input-group-intolerance"
      label-cols-sm="3"
      label="intolerance:"
      label-for="intolerance"
      >
        <b-form-select
            id="intolerance"
            v-model="form.intolerance"
            :disabled="!form.filter"
            :options="intolerances"
        ></b-form-select>
      </b-form-group>

        
        
        
        

        <b-button type="reset" variant="danger">Reset</b-button>
        <b-button
          type="submit"
          variant="primary"
          style="width:250px;"
          class="ml-5 w-75"
          >Search</b-button
        >
      </b-form>
      <b-alert
      class="mt-2"
      v-if="form.submitError"
      variant="warning"
      dismissible
      show
    >
      Search failed: {{ form.submitError }}
    </b-alert>
    <!-- <b-card class="mt-3 md-3" header="Form Data Result">
      <pre class="m-0"><strong>form:</strong> {{ form }}</pre>
      <pre class="m-0"><strong>$v.form:</strong> {{ $v.form }}</pre>
    </b-card> -->
    <RecipePreviewList v-if="hasSearch" :key="key" title="Search result" v-bind:path="searchPath"></RecipePreviewList> 
  </div>
</template>

<script>
import cuisines from "../assets/cuisine";
import diets from "../assets/diets";
import intolerances from "../assets/intolerances";
import RecipePreviewList from "../components/RecipePreviewList";
import { required } from "vuelidate/lib/validators";

export default {
  name: "Search",
  components: {
    RecipePreviewList
},
  data() {
    return {
      form: {
        nameRecipe: "",
        amount: null,
        sort: null,
        filter: false,
        cuisine: null,
        diet: null,
        intolerance: null,
        submitError: undefined
      },
      sort_options: [
        {value: 'popularity', text: 'popularity'},
        {value: 'time', text: 'time'},
      ],
      amount_options: [
        {value: '5', text: '5'},
        {value: '10', text: '10'},
        {value: '15', text: '15'},
      ],
      errors: [],
      filter: "false",
      validated: false,
      cuisines: [{ value: null, text: "", disabled: true }],
      diets: [{ value: null, text: "", disabled: true }],
      intolerances: [{ value: null, text: "", disabled: true }],
      hasSearch: false,
      searchPath: "",
      param: "",
      key: 0,
    };
  },
  validations: {
    form: {
      nameRecipe: {
        required
      },
      amount: {
        required
      },
      sort: {
        required
      }
    }
  },
  mounted() {
    // console.log("mounted");
    this.cuisines.push(...cuisines);
    this.diets.push(...diets);
    this.intolerances.push(...intolerances);
    // console.log($v);
  },
  methods: {
    validateState(param) {
      const { $dirty, $error } = this.$v.form[param];
      return $dirty ? !$error : null;
    },
    async search() {
      try {
        this.form.cuisine = this.form.cuisine==null ? "None" : this.form.cuisine;
        this.form.diet = this.form.diet==null ? "None" : this.form.diet;
        this.form.intolerance = this.form.intolerance==null ? "None" : this.form.intolerance;
        this.form.filter = this.form.filter==false ? "0" : "1";

        this.param = "name="+ this.form.nameRecipe + "&amount=" + this.form.amount+"&filter=" + 
        this.form.filter+"&diet="+this.form.diet+"&cuisine="+this.form.cuisine+"&intolerances="+
        this.form.intolerance+"&sort="+this.form.sort;
        
        this.searchPath = "/recipes/search?" + this.param;
        this.hasSearch = true;
        console.log(this.searchPath);
        this.key +=1;

      } catch (err) {
        console.log(err.response);
        this.form.submitError = err.response.data.message;
      }
    },
      onSearch() {
      // console.log("search method called");
      this.$v.form.$touch();
      if (this.$v.form.$anyError) {
        return;
      }
      // console.log("search method go");
      this.search();
    },
    onReset() {
      this.form = {
        nameRecipe: "",
        amount: null,
        sort: null,
        filter: false,
        cuisine: null,
        diet: null,
        intolerance: null,
      };
      this.$nextTick(() => {
        this.$v.$reset();
      });
    }
  }
};
</script>
<style lang="scss" scoped>
.container {
  max-width: 500px;
}
</style>
