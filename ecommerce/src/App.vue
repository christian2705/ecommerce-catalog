<script setup>
import { RouterView } from 'vue-router';
import LayoutBackgroud from './components/layout/LayoutBackground.vue';
// import NavigationBar from './components/common/NavigationBar.vue';
import BreadCrumbs from './components/common/BreadCrumbs.vue';
</script>

<template>
  <LayoutBackgroud>
    <template #navbar> </template>
  </LayoutBackgroud>

  <RouterView />
</template>

<style scoped>
@media screen and (min-width: 1024px) {
  .navbar__bread {
    display: none;
  }
}
</style>
