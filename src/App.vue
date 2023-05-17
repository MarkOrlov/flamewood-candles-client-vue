<template lang="">
  <div class="main-wrapper">
    <Header></Header>
    <Body></Body>
    <Footer></Footer>
  </div>

</template>

<script>

import Header from './components/Header/HeaderView.vue';
import Body from './components/Body/BodyView.vue';
import Footer from './components/Footer/FooterView.vue';
export default {
  components: {
    Header,
    Body,
    Footer
  },
}
</script>

<style>
.main-wrapper {
  display: flex;
  flex-direction: column;
  justify-content: space-between;
}
</style>