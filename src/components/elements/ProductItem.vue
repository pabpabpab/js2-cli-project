<template>
  <div :class="oneProductClass">
    <router-link class="featured__one__imgLink" :to="'/product/' + product.id_product">
      <img :src="imgPath + product.img[0]" alt="product" class="featured__one__img">
    </router-link>
    <div class="featured__one__textdiv">
      <router-link class="featured__one__nameLink" :to="'/product/' + product.id_product">
        {{product.product_name}}
      </router-link>
      <p class="featured__one__price">${{product.price}}</p>
    </div>
    <a href="#" class="featured__one__whiteCart__link" @click.prevent="cartAPI.addProduct(product)">
      <img :src="imgPath + 'white_cart.svg'" alt="cart" class="white_cart_img">
      Add to Cart
    </a>
    <div class="good_quantity_control" v-if="cartAPI.getProductQuantity(product)">
      <div class="good_quantity_control__btn_left" @click="cartAPI.addProduct(product)">+</div>
      <div class="good_quantity_control__value">{{cartAPI.getProductQuantity(product)}}</div>
      <div class="good_quantity_control__btn_right" @click="cartAPI.remove(product)">-</div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'ProductItem',
  props: ['product', 'mode'],

  data() {
    return {
      cartAPI: this.$parent.$parent.$parent.$refs.header.$refs.cart,
      imgPath: 'http://localhost:8080/img/',
      oneProductClass: '',
    };
  },

  mounted() {
    switch (this.mode) {
      case 'featured-products':
        this.oneProductClass = 'featured__one';
        break;
      case 'you-may-like-also':
        this.oneProductClass = 'featured__one also__one';
        break;
      default:
        this.oneProductClass = 'featured__one';
    }
  },

};
</script>
