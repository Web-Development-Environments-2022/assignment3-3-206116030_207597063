<template>
    <div class="container">
        <h1 class="title">Add Recipe</h1>
        <b-form @submit.prevent="onSaveRecipe" @reset.prevent="onReset">
            <b-form-group id="input-group-name" label-cols-sm="3" label="Name:" label-for="name">
                <b-form-input id="name" v-model="$v.form.name.$model" type="text" :state="validateState('name')">
                </b-form-input>
                <b-form-invalid-feedback v-if="!$v.form.name.required">
                Name is required
                </b-form-invalid-feedback>
                <b-form-invalid-feedback v-if="!$v.form.name.alpha">
                Name alpha
                </b-form-invalid-feedback>
            </b-form-group>
            <b-form-group id="input-group-image" label-cols-sm="3" label="Image path:" label-for="imagePath">
                <b-form-input id="imagePath" v-model="$v.form.image.$model" type="text" :state="validateState('image')">
                </b-form-input>
                <b-form-invalid-feedback v-if="!$v.form.image.required">
                Image is required
                </b-form-invalid-feedback>
            </b-form-group>   
            <b-form-group id="input-group-readyInMinutes" label-cols-sm="3" label="Ready in minutes:" label-for="readyInMinutes">
                <b-form-input id="readyInMinutes" v-model="$v.form.readyInMinutes.$model" type="number" :state="validateState('readyInMinutes')">
                </b-form-input>
                <b-form-invalid-feedback v-if="!$v.form.readyInMinutes.required">
                Ready in minutes is required
                </b-form-invalid-feedback>
                <b-form-invalid-feedback v-if="!$v.form.readyInMinutes.numeric">
                Ready in minutes must be a numeric
                </b-form-invalid-feedback>
            </b-form-group>  
            <b-form-group id="input-group-vegan" label-cols-sm="3" label="vegan:" label-for="vegan">
                <input type="radio" id="vegan-true" value="1" v-model="$v.form.vegan.$model" />
                <label for="one">True</label>

                <input type="radio" id="vegan-false" value="0" v-model="$v.form.vegan.$model"/>
                <label for="two">False</label>
            </b-form-group>
            <b-form-group id="input-group-vegeterian" label-cols-sm="3" label="vegeterian:" label-for="vegeterian">
                <input type="radio" id="vegeterian-true" value="1" v-model="$v.form.vegeterian.$model" />
                <label for="one">True</label>

                <input type="radio" id="vegeterian-false" value="0" v-model="$v.form.vegeterian.$model"/>
                <label for="two">False</label>
            </b-form-group>
            <b-form-group id="input-group-glutenFree" label-cols-sm="3" label="glutenFree:" label-for="glutenFree">
                <input type="radio" id="glutenFree-true" value="1" v-model="$v.form.glutenFree.$model" />
                <label for="one">True</label>

                <input type="radio" id="glutenFree-false" value="0" v-model="$v.form.glutenFree.$model"/>
                <label for="two">False</label>
            </b-form-group>
            <b-form-group id="input-group-servings" label-cols-sm="3" label="How many servings?" label-for="servings">
                <b-form-input id="servings" v-model="$v.form.servings.$model" type="number" :state="validateState('servings')">
                </b-form-input>
                <b-form-invalid-feedback v-if="!$v.form.servings.required">
                Servings is required
                </b-form-invalid-feedback>
                <b-form-invalid-feedback v-if="!$v.form.servings.numeric">
                Servings must be a numeric
                </b-form-invalid-feedback>
            </b-form-group>  
            <b-form-group id="input-group-analyzedInstructions" label-cols-sm="3" label="analyzed instructions:" label-for="analyzedInstructions">
                <b-form-input id="analyzedInstructions" v-model="$v.form.analyzedInstructions.$model" type="text" :state="validateState('analyzedInstructions')">
                </b-form-input>
                <b-form-invalid-feedback v-if="!$v.form.analyzedInstructions.required">
                analyzedInstructions is required
                </b-form-invalid-feedback>
            </b-form-group>
            
            
            <b-button type="reset" variant="danger">Reset</b-button>
            <b-button type="submit" variant="primary" style="width:250px;" class="ml-5 w-75">Save recipe</b-button>
        </b-form>

        <b-alert class="mt-2" v-if="form.submitError" variant="warning" dismissible show>
            Add recipe failed: {{ form.submitError }}
        </b-alert>
        <b-card class="mt-3 md-3" header="Form Data Result">
            <pre class="m-0"><strong>form:</strong> {{ form }}</pre>
            <pre class="m-0"><strong>$v.form:</strong> {{ $v.form }}</pre>
        </b-card>
        
    </div>
</template>

<script>
import {
  required,
  alpha,
  numeric
} from "vuelidate/lib/validators";
export default {
    data() {
        return {
            form: {
                name: "",
                image: "",
                readyInMinutes: "",
                vegan: "0",
                vegeterian: "0",
                glutenFree: "0",
                servings: "",
                analyzedInstructions: "",
                submitError: undefined
            },
            errors: [],
            validated: false
        };
    },
    validations: {
    form: {
      name: {
        required,
        alpha
      },
      image: {
        required
      },
      readyInMinutes: {
        required,
        numeric
      },
      servings: {
        required,
        numeric
      },
      vegan: {
        required
      },
      vegeterian: {
        required
      },
      glutenFree: {
        required
      },   
      analyzedInstructions:{
        required
      },
    }
  },
  methods: {
    validateState(param) {
      const { $dirty, $error } = this.$v.form[param];
      return $dirty ? !$error : null;
    },
    async Save() {
        try {
        const response = await this.axios.post(
          "http://localhost:3000/recipe/addRecipe",
          //this.$root.store.server_domain + "/recipe/addRecipe",

          {
            title: this.form.name,
            recipeImage: this.form.image,
            readyInMinutes: this.form.readyInMinutes,
            vegen: this.form.vegen,
            vegeterian: this.form.vegeterian,
            glutenFree: this.form.glutenFree,
            servings: this.form.servings,
            analyzedInstructions: this.form.analyzedInstructions,
            ingredients: ""
          }
        );
        this.$router.push("/search");
        // console.log(response);
      } catch (err) {
        console.log(err.response);
        this.form.submitError = err.response.data.message;
      }
    },
    onSaveRecipe() {
      // console.log("onSaveRecipe method called");
      this.$v.form.$touch();
      if (this.$v.form.$anyError) {
        return;
      }
      // console.log("Save method go");
      this.Save();
    },
    onReset() {
      this.form = {
        name: "",
        image: "",
        readyInMinutes: "",
        vegan: "0",
        vegeterian: "0",
        glutenFree: "0",
        servings: "",
        analyzedInstructions: "",
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