<template>

  <div id="app">
    <b-navbar type="dark" variant="dark">
    <b-navbar-nav>
      <b-nav-item :to="{ name: 'main' }"><b-icon class="icons" icon="house-fill"></b-icon>Home</b-nav-item>
      <b-nav-item :to="{ name: 'search' }"><b-icon class="icons" icon="search"></b-icon>Search</b-nav-item>
      <b-nav-item :to="{ name: 'about' }"><b-icon class="icons" icon="info-circle-fill"></b-icon>About</b-nav-item>
      <b-nav-item :to="{ name: 'OurFamilyRecipes' }"><b-icon class="icons" icon="newspaper"></b-icon>Family Recipes</b-nav-item>

    </b-navbar-nav>
    <b-navbar-nav class="ml-auto">
  
      <b-nav-text v-if="!$root.store.username">Hello Guest: </b-nav-text>
      <b-nav-item v-if="!$root.store.username" :to="{ name: 'register' }"><b-icon class="icons" icon="person-plus-fill"></b-icon>Register</b-nav-item>
      <b-nav-item v-if="!$root.store.username" :to="{ name: 'login' }"><b-icon class="icons" icon="person-circle"></b-icon>Login</b-nav-item>
      
      <!-- Navbar dropdowns -->
      <b-nav-item-dropdown text="My Kitchen" v-if="$root.store.username" right>
        <b-dropdown-item :to="{ name: 'favorites' }"><b-icon class="icons" icon="star-fill"></b-icon>Favorites</b-dropdown-item>
        <b-dropdown-item :to="{ name: 'myRecipes' }"><b-icon class="icons" icon="list-check"></b-icon>My recipes</b-dropdown-item>
        <b-dropdown-item v-if="$root.store.username" id="show-btn" @click="$bvModal.show('bv-modal-example')">
          <b-icon class="icons" icon="pencil-square"></b-icon>Add new recipe </b-dropdown-item>
          <b-modal id="bv-modal-example" hide-footer>
            <template #modal-title>
              Add Recipe
            </template>
            <div class="d-block text-center">
              <addRecipe></addRecipe>
            </div>
            <b-button class="mt-3" block @click="$bvModal.hide('bv-modal-example')">Cancel</b-button>
          </b-modal>
      </b-nav-item-dropdown>
      <b-nav-item-dropdown :text="$root.store.username" v-if="$root.store.username" right>
        <b-dropdown-item v-on:click="Logout" :to="{ name: 'main' }"><b-icon icon="person-circle"></b-icon>
        Sign Out</b-dropdown-item>
      </b-nav-item-dropdown>
    </b-navbar-nav>
  </b-navbar>
    <router-view />
  </div>
</template>

<script>
import addRecipe from "./components/AddRecipeCompo.vue";
export default {
  name: "App",
  components: {
    addRecipe
  },
  data() {
    return {
      username: this.$root.store.username,
    };
  },
  methods: {
    Logout() {
      this.$root.store.logout();
      this.$root.toast("Logout", "User logged out successfully", "success");
      localStorage.setItem("lastSearch", '');
      this.$router.push("/").catch(() => {
        this.$forceUpdate();
      });
    }
  }
};
</script>

<style lang="scss">
@import "@/scss/form-style.scss";
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  min-height: 100vh;
}
#nav {
  padding: 30px;
}
#nav a {
  font-weight: bold;
  color: #2c3e50;
}
#nav a.router-link-exact-active {
  color: #42b983;
}
.icons{
  margin-right:7px;
}
</style>