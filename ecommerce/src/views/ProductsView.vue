<script setup>
import { onBeforeMount } from 'vue';
import { computed } from 'vue';
import { storeToRefs } from 'pinia';
import { useRouter, useRoute, RouterView } from 'vue-router';
import useProductStore from '../stores/product';
import useCountProductStore from '../stores/countProduct';
import WrapperButton from '../components/common/button/WrapperButton.vue';
import ButtonAction from '../components/common/button/ButtonAction.vue';
import ButtonPagnation from '../components/common/button/ButtonPagnation.vue';
import BreadCrumbs from '../components/common/BreadCrumbs.vue';
import ProductRating from '../components/common/product/ProductRating.vue';
import ProductReview from '../components/common/product/ProductReview.vue';
import LayoutProduct from '../components/layout/LayoutProduct.vue';
import SkeletonUI from '../components/common/SkeletonUI.vue';
import ErrorUI from '../components/common/ErrorUI.vue';

defineEmits(['pointerenter', 'pointerleave']);

const productStore = useProductStore();
const countProductStore = useCountProductStore();
const router = useRouter();
const route = useRoute();

const { product, cartProducts, isLoading, isAvailableProduct, backgroundProduct } = storeToRefs(productStore);
const { countProduct } = storeToRefs(countProductStore);

const category = computed(() => (isAvailableProduct.value ? product.value?.category.split("'s ").join('-') : 'unvailable-product'));
const isActiveCart = computed(() => cartProducts.value?.some((cart) => cart.productId === `id-${product.value?.id}` && cart.isProductCart));

onBeforeMount(() => {
  const id = route.params.id ? route.params.id : countProduct.value;
  if (route.params.id) countProductStore.updateCountByParamsId(Number(id));

  productStore.getProductByCount(id);
  productStore.setBackgroundProduct(category);
});

const pagnationNextProduct = () => {
  countProductStore.nextProductCount();
  productStore.getProductByCount(countProduct.value);
  productStore.setBackgroundProduct(category);

  router.push(`/products/${countProduct.value}`);
};

const pagnationPreviousProduct = () => {
  countProductStore.previousProductCount();
  productStore.getProductByCount(countProduct.value);
  productStore.setBackgroundProduct(category);

  router.push(`/products/${countProduct.value}`);
};

const addProductToCart = () => productStore.addToCart(product.value?.id, 1);
</script>

<template>
  <SkeletonUI v-if="isLoading" />

  <LayoutProduct v-if="!isLoading && !isAvailableProduct" type="errorLayout" :pagnationPrevious="pagnationPreviousProduct" :pagnationNext="pagnationNextProduct">
    <template #error-product>
      <ErrorUI errorMsg="This product is unavailable to show" />
    </template>
  </LayoutProduct>

  <LayoutProduct v-if="!isLoading && isAvailableProduct" :pagnationPrevious="pagnationPreviousProduct" :pagnationNext="pagnationNextProduct">
    <template #left-product>
      <div class="left-product">
        <img class="product__img" data-testid="test-render-image" :src="product.image" :alt="product.title" />
      </div>
    </template>
    <template #right-product>
      <div class="right-product">
        <div class="product__heading" data-testid="test-render-title">
          {{ product.title }}
          <div class="product__category" :class="backgroundProduct.value">
            <h3 data-testid="test-render-category">{{ product.category }}</h3>
          </div>
          <div class="product__rating" :class="backgroundProduct.value">
            <h3 data-testid="test-render-category">{{ product.rating.rate }}/5</h3>
          </div>
        </div>
        <p class="product__desc">{{ product.description }}</p>
        <h1>${{ product.price }}</h1>

        <WrapperButton :opsi="2">
          <h1 slot="product-price" class="product__price">${{ product.price }}</h1>
          <template #button-opsi-1>
            <ButtonAction data-test-id="add-to-cart" :handlerEvent="addProductToCart" :isActiveCart="isActiveCart" iconName="ShoppingCart" action="Add to Cart" />
          </template>
        </WrapperButton>
      </div>
    </template>
  </LayoutProduct>

  <RouterView />
</template>

<style>
.left-product {
  display: flex;
  justify-content: center;
  align-items: center;
  width: 240px;
  height: 350px;
  margin: 0 auto;
}

.product__group {
  display: flex;
  justify-content: flex-start;
  align-items: center;
  gap: 0.5em;
}

.product__group.group__gap {
  gap: 1.2em;
}

.product__text {
  font-style: normal;
  font-size: var(--fs-text-md);
  font-weight: var(--font-regular);
}

.product__heading {
  font-style: normal;
  font-size: var(--fs-heading);
  font-weight: var(--font-semi-bold);
  line-height: 30px;
  margin-bottom: 0.5em;
  border-bottom: 1px solid rgba(50, 48, 50, 0.32);
}

.product__category {
  display: flex;
  justify-content: center;
  align-items: center;
  width: 180px;
  height: 45px;
  margin: 1.6em 0 0.65em;
}

.product__category h3 {
  font-style: normal;
  font-size: var(--fs-text-lg);
  font-style: italic;
  font-weight: var(--font-medium);
}

.product__rating {
  display: flex;
  justify-content: center;
  align-items: center;
  width: 45px;
  height: 22px;
}

.product__rating h3 {
  font-style: normal;
  font-size: var(--fs-text-lg);
  font-weight: var(--font-medium);
}

.product__desc {
  font-style: normal;
  font-size: var(--fs-text-sm);
  font-weight: var(--font-regular);
  line-height: 21px;
  margin-bottom: 0.65em;
  border-bottom: 1px solid rgba(50, 48, 50, 0.32);
}

.product__price-desktop {
  display: none;
}

.icon-product {
  width: 20px;
  height: 20px;
}

@media screen and (min-width: 1024px) {
  .left-product {
    width: 320px;
    height: 510px;
  }

  .icon-product {
    width: 24px;
    height: 24px;
  }

  .product__desc {
    font-size: var(--fs-text-lg);
    line-height: 30px;
  }

  .product__category {
    width: 220px;
  }

  .product__breadcrumbs {
    display: block;
    margin-bottom: 1em;
  }
}

@media screen and (min-width: 1250px) {
  .left-product {
    width: 35%;
    height: 100%;
  }

  .right-product {
    position: relative;
    width: 65%;
    height: 457px;
  }

  .product__img {
    max-width: 100%;
  }

  .product__price-mobile,
  .product__group.product__group-mobile {
    display: none;
  }

  .product__category {
    display: block;
    width: 50%;
    background: transparent;
  }

  .product__category h3 {
    color: var(--primary-purple);
  }

  .product__price-desktop {
    display: block;
    width: 185px;
    height: 44px;
    border: 2px solid var(--primary-purple);
    border-radius: var(--radius-circle);
  }

  .product__price-desktop p {
    font-style: normal;
    font-weight: var(--font-semi-bold);
    font-size: var(--fs-text-lg);
    text-align: center;
    line-height: 44px;
    color: var(--primary-purple);
  }

  .product__desc {
    width: 90%;
    height: 180px;
    margin-bottom: 0;
    overflow: hidden;
  }
}
</style>
