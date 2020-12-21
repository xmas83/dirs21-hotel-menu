<template>
  <v-app>
    <div class="banner-text">
      <ul>
        <li>Home</li>
        <li>About</li>
        <li>Services</li>
        <li>FAQ</li>
        <li>Contact</li>
      </ul>
    </div>
    <div class="animation-area">
    </div>
    <v-main class="bg">
      <Dishes class="container"/>
    </v-main>
  </v-app>
</template>

<script>
import Dishes from './components/Dishes';

export default {
  name: 'App',

  components: {
    Dishes,
  },

  data: () => ({
    //
  }),
};
</script>

<style>
.bg{
  background: url("./assets/epic.jpg");
  background-size: cover;
}
.container{
  margin-top: 30px;
}
  .banner-text{
    width: 100%;
    position: absolute;
  }
  .banner-text ul{
    height: 50px;
    float: right;
  }
  .banner-text ul li{
    display: inline-block;
    padding: 40px 15px;
    text-transform: uppercase;
    color: #fff;
    font-size: 15px;
  }
  .animation-area{
    background: linear-gradient(to left,#19222A,#636878);
    width: 100%;
    height: 10vh;
  }
</style>