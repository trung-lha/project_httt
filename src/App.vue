<template>
  <v-app>
    <v-main class="stylemain">
      <div :style="`background-image: url(${srcImg})`" style=" background-position:center; background-size: cover; width: 100%; position: absolute; top: 0px; left: 0px;">
      <HelloWorld 
      @change="getImg"
      />
      </div>
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
    srcImg: '/img/afterglow.7b730791.jpg'
    //
  }),
  methods: {
    getImg(value){
      // console.log(value);
      if (!value) {
        this.srcImg = '/img/afterglow.7b730791.jpg'
        console.log(this.srcImg,"true");
      } else {
        this.srcImg = '/img/1076.07102d5e.jpg'
        console.log(this.srcImg,"false");
      }
    }
  }
};
</script>

<style lang="scss" scoped>

</style>
