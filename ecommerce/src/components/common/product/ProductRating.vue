<script setup>
import { v4 as uuidv4 } from 'uuid';
import { computed } from 'vue';
// import { Star1 } from 'vue-iconsax';

const props = defineProps({
  rating: Number,
});
</script>

<template>
  <div class="product__group">
    <div class="star__group"></div>
    <h5 class="product__text">{{ rating }}/5</h5>
  </div>
</template>

<style scoped>
.star__group {
  display: flex;
  justify-content: space-between;
  align-items: center;
}
</style>
