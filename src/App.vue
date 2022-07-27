<template>

  <div id="app">
    <b-navbar type="dark" variant="dark">
    <b-navbar-nav>
      <b-nav-item :to="{ name: 'main' }">Home</b-nav-item>
      <b-nav-item :to="{ name: 'search' }">Search</b-nav-item>
      <b-nav-item :to="{ name: 'about' }">About</b-nav-item>
      <b-nav-item :to="{ name: 'OurFamilyRecipes' }">Family Recipes</b-nav-item>
      <b-nav-text v-if="!$root.store.username">Hello Guest: </b-nav-text>
      <b-nav-item v-if="!$root.store.username" :to="{ name: 'register' }">Register</b-nav-item>
      <b-nav-item v-if="!$root.store.username" :to="{ name: 'login' }">Login</b-nav-item>
      
      <!-- Navbar dropdowns -->
      <b-nav-item-dropdown text="My Kitchen" v-if="$root.store.username" right>
        <b-dropdown-item :to="{ name: 'search' }">Favorites</b-dropdown-item>
        <b-dropdown-item :to="{ name: 'search' }">My recipes</b-dropdown-item>
      </b-nav-item-dropdown>

      <b-nav-item v-if="$root.store.username" id="show-btn" @click="$bvModal.show('bv-modal-example')"> addRecipe </b-nav-item>
      <b-modal id="bv-modal-example" hide-footer>
        <template #modal-title>
          Add Recipe
        </template>
        <div class="d-block text-center">
          <addRecipe></addRecipe>
        </div>
        <b-button class="mt-3" block @click="$bvModal.hide('bv-modal-example')">Close Me</b-button>
      </b-modal>


      <b-nav-item-dropdown :text="username" v-if="$root.store.username" right>
        <b-dropdown-item v-on:click="Logout" :to="{ name: 'main' }">Sign Out</b-dropdown-item>
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
</style>