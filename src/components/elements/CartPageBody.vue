<template>
  <div>
    <template v-if="cartAPI.goodsQuantity">
      <cart-page-body-item
        v-for="item of cartAPI.cartItems"
        :key="item.id_product"
        :cart-item="item">
      </cart-page-body-item>

      <div class="container shoppingCart_clear_and_continue">
        <a href="#" @click.prevent="cartAPI.clearCart()" class="shoppingCart_clear">
          cLEAR SHOPPING CART
        </a>
        <router-link class="shoppingCart_continue" to="/catalog">
          cONTINUE sHOPPING
        </router-link>
      </div>
    </template>
    <div class="shoppingCart_is_empty" v-else>
      the cart is empty
    </div>
  </div>
</template>

<script>
import CartPageBodyItem from '@/components/elements/CartPageBodyItem.vue';

export default {
  name: 'CartPageBody',
  components: { CartPageBodyItem },
  data() {
    return {
      cartAPI: this.$parent.$parent.$refs.header.$refs.cart,
    };
  },
};
</script>
