<template>
  <v-app>
    <v-main class="stylemain">
      <HelloWorld />
    </v-main>
  </v-app>
</template>

<script>
import HelloWorld from "./components/HelloWorld";

export default {
  name: "App",

  components: {
    HelloWorld,
  },

  data: () => ({
    //
  }),
};
</script>

<style lang="scss" scoped>
.stylemain {
  background-image: url("./assets/soicttet2021.jpg");
  // background-image: url("./assets/1076.jpg");
  // background-color:rgb(90, 236, 229);
  background-position:center;
  background-size: cover;
}
</style>
