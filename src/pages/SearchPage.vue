<template>
  <div class="container">
    <h1 class="title">Search Page</h1>
    <b-form @submit.prevent="onSearch" @reset.prevent="onReset">

      <b-form-group id="input-group-name" label-cols-sm="3" label="nameRecipe:" label-for="nameRecipe" v-if="!ShowingResults">
        <b-form-input id="nameRecipe" v-model="form.nameRecipe" type="text" :state="validateState('nameRecipe')">
        </b-form-input>
        <b-form-invalid-feedback v-if="!$v.form.nameRecipe.required">
          Recipe's name is required
        </b-form-invalid-feedback>
      </b-form-group>

      <b-form-group id="input-group-amount" label-cols-sm="3" label="amount:" label-for="amount" v-if="!ShowingResults">
        <b-form-select id="amount" v-model="form.amount" :options="amount_options" :state="validateState('amount')">
        </b-form-select>
        <b-form-invalid-feedback v-if="!$v.form.amount.required">
          amount is required
        </b-form-invalid-feedback>
      </b-form-group>

      <b-form-group id="input-group-sort" label-cols-sm="3" label="sort:" label-for="sort" v-if="!ShowingResults">
        <b-form-select id="sort" v-model="form.sort" :options="sort_options" :state="validateState('sort')">
        </b-form-select>
        <b-form-invalid-feedback v-if="!$v.form.sort.required">
          sort is required
        </b-form-invalid-feedback>
      </b-form-group>

      <b-form-group id="input-group-filter" label-cols-sm="3" label="filter:" label-for="filter" v-if="!ShowingResults">
        <input class="form-check-input" v-model="form.filter" type="checkbox" id="filter" />
      </b-form-group>

      <b-form-group id="input-group-cuisine" label-cols-sm="3" label="cuisine:" label-for="cuisine" v-if="!ShowingResults">
        <b-form-select id="cuisine" v-model="form.cuisine" :disabled="!form.filter" :options="cuisines"></b-form-select>
      </b-form-group>

      <b-form-group id="input-group-diet" label-cols-sm="3" label="diet:" label-for="diet" v-if="!ShowingResults">
        <b-form-select id="diet" v-model="form.diet" :disabled="!form.filter" :options="diets"></b-form-select>
      </b-form-group>

      <b-form-group id="input-group-intolerance" label-cols-sm="3" label="intolerance:" label-for="intolerance" v-if="!ShowingResults">
        <b-form-select id="intolerance" v-model="form.intolerance" :disabled="!form.filter" :options="intolerances">
        </b-form-select>
      </b-form-group>

      <b-button type="reset" variant="danger" v-if="!ShowingResults">Reset</b-button>
      <b-button type="submit" variant="primary" style="width:250px;" class="ml-5 w-75" v-if="!ShowingResults">Search</b-button>
      <b-button type="button" variant="outline-primary" v-on:click="showLastSearch" v-if="($root.store.username) && (!lastSearch=='') && (!ShowingResults)">Last search</b-button>
    </b-form>
    <b-alert class="mt-2" v-if="form.submitError" variant="warning" dismissible show>
      Search failed: {{ form.submitError }}
    </b-alert>
    <!-- <b-card class="mt-3 md-3" header="Form Data Result">
      <pre class="m-0"><strong>form:</strong> {{ form }}</pre>
      <pre class="m-0"><strong>$v.form:</strong> {{ $v.form }}</pre>
    </b-card> -->
    <b-button type="submit" v-on:click="ShowingResults=false" variant="primary" style="width:250px;" class="ml-5 w-75" v-if="ShowingResults">Back To Search</b-button>
    <RecipePreviewList v-if="ShowingResults" :key="key" title="Search result" v-bind:path="searchPath"></RecipePreviewList>
    <b-button type="submit" v-on:click="ShowingResults=false" variant="primary" style="width:250px;" class="ml-5 w-75" v-if="ShowingResults">Back To Search</b-button>
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
        cuisine: '',
        diet: '',
        intolerance: '',
        submitError: undefined,
      },
      sort_options: [
        { value: 'popularity', text: 'popularity' },
        { value: 'time', text: 'time' },
      ],
      amount_options: [
        { value: '5', text: '5' },
        { value: '10', text: '10' },
        { value: '15', text: '15' },
      ],
      lastSearch: localStorage.getItem("lastSearch"),
      ShowingResults: false,
      errors: [],
      //filter: false,
      validated: false,
      cuisines: [{ value: null, text: "", disabled: true }],
      diets: [{ value: null, text: "", disabled: true }],
      intolerances: [{ value: null, text: "", disabled: true }],
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
        this.form.cuisine = this.form.cuisine == '' ? "None" : this.form.cuisine;
        this.form.diet = this.form.diet == '' ? "None" : this.form.diet;
        this.form.intolerance = this.form.intolerance == '' ? "None" : this.form.intolerance;
        this.form.filter = this.form.filter == false ? "0" : "1";

        this.param = "name=" + this.form.nameRecipe + "&amount=" + this.form.amount + "&filter=" +
          this.form.filter + "&diet=" + this.form.diet + "&cuisine=" + this.form.cuisine + "&intolerances=" +
          this.form.intolerance + "&sort=" + this.form.sort;

        this.searchPath = "/recipes/search?" + this.param;
        this.ShowingResults = true;
        console.log(this.searchPath);
        this.key += 1;

        const last = {
          'nameRecipe': this.form.nameRecipe,
          'amount': this.form.amount,
          'sort': this.form.sort,
          'filter': this.form.filter == 0 ? false: true ,
          'cuisine': this.form.cuisine,
          'diet': this.form.diet,
          'intolerance': this.form.intolerance,
          'submitError': this.form.submitError
        };
        localStorage.setItem("lastSearch", JSON.stringify(last));
        this.lastSearch = JSON.parse(localStorage.getItem("lastSearch"))

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
    },
    showLastSearch(){
      this.lastSearch = JSON.parse(localStorage.getItem("lastSearch"))
      this.form = {
        nameRecipe: this.lastSearch.nameRecipe,
        amount: this.lastSearch.amount,
        sort: this.lastSearch.sort,
        filter: this.lastSearch.filter,
        cuisine: this.lastSearch.cuisine,
        diet: this.lastSearch.diet,
        intolerance: this.lastSearch.intolerance,
      };
    }
  }
};
</script>
<style lang="scss" scoped>
.container {
  max-width: 500px;
}
</style>
