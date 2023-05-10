<template lang="">
  <Header></Header>
  <Body></Body>

  <select>
    <option value="1">Свеча 50мл</option>
    <option value="2">Свеча 100мл</option>
    <option value="3">Аромадиффузор домашний</option>
    <option value="4">Аромадиффузор автомобильный</option>
  </select>
</template>

<script>

import Header from './components/Header/HeaderView.vue';
import Body from './components/Body/BodyView.vue';
export default {
  components: {
    Header,
    Body
  },
}
</script>

<style lang="">
  
</style>