<script setup>
// import { Message } from 'vue-iconsax';

defineProps({
  count: Number,
});
</script>

<template>
  <div class="product__group">
    <h5 class="product__text">{{ count }} Ulasan</h5>
  </div>
</template>
