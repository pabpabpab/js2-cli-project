<template>
  <div>
    <router-link class="cart_link" to="/cart">
      <img :src="imgPath + 'cart.svg'" alt="cart" class="cart">
      <div class="cart_quantity" v-show="goodsQuantity">{{ goodsQuantity }}</div>
    </router-link>
    <div class="drop_cart">
      <template v-if="cartItems.length">
        <drop-cart-item
          v-for="item of cartItems"
          :key="item.id_product"
          :cart-item="item"
          @remove="remove">
        </drop-cart-item>

        <div class="drop_cart_totalPrice">
          <p class="drop_cart_totalPrice_left">TOTAL</p>
          <p class="drop_cart_totalPrice_right">${{ totalPrice }}</p>
        </div>

        <router-link class="drop_cart_checkOut" to="/checkout">
          Checkout
        </router-link>
        <router-link class="drop_cart_goToCart" to="/cart">
          Go to cart
        </router-link>
      </template>

      <div class="drop_cart_empty" v-else>
        Корзина пуста
      </div>
    </div>
  </div>
</template>

<script>
import DropCartItem from '@/components/elements/DropCartItem.vue';

export default {
  name: 'DropCart',
  components: { DropCartItem },

  data() {
    return {
      cartItems: [],
      showCart: false,
      imgPath: 'http://localhost:8080/img/',
    };
  },

  methods: {
    addProduct(product) {
      const find = this.cartItems.find((el) => el.id_product === product.id_product);
      if (find) {
        this.$parent.$parent.putJson(`/api/cart/${find.id_product}`, { quantity: 1 })
          .then((data) => {
            if (data.result === 1) {
              find.quantity = data.newQuantity; // реальное кол-во товара с сервера
            }
          });
      } else {
        const prod = { ...product, ...{ quantity: 1 } };
        this.$parent.$parent.postJson('/api/cart', prod)
          .then((data) => {
            if (data.result === 1) {
              this.cartItems.push(prod);
            }
          });
      }
    },

    remove(item) {
      const find = this.cartItems.find((el) => el.id_product === item.id_product);
      if (!find) return;

      if (find.quantity > 1) {
        this.$parent.$parent.putJson(`/api/cart/${find.id_product}`, { quantity: -1 })
          .then((data) => {
            if (data.result === 1) {
              find.quantity = data.newQuantity; // реальное кол-во товара с сервера
            }
          });
      } else {
        this.$parent.$parent.deleteJson(`/api/cart/${find.id_product}`)
          .then((data) => {
            if (data.result === 1) {
              // на всякий случай еще раз проверка на реальное кол-во товара с сервера
              if (data.newQuantity < 1) {
                this.cartItems.splice(this.cartItems.indexOf(find), 1);
              } else {
                find.quantity = data.newQuantity;
              }
            }
          });
      }
    },

    // ввод кол-ва в input на странице корзины
    inputProductQuantity(item, quantity) {
      const find = this.cartItems.find((el) => el.id_product === item.id_product);
      if (!find) return;

      if (quantity > 0) {
        this.$parent.$parent.putJson(`/api/cart/${find.id_product}`, { newQuantityValue: quantity })
          .then((data) => {
            if (data.result === 1) {
              // на всякий случай присваиваем реальное кол-во товара с сервера
              find.quantity = data.newQuantity;
            }
          });
      } else {
        this.$root.deleteJson(`/api/cart/${find.id_product}`)
          .then((data) => {
            if (data.result === 1) {
              // на всякий случай еще раз проверка на реальное кол-во товара с сервера
              if (data.newQuantity < 1) {
                this.cartItems.splice(this.cartItems.indexOf(find), 1);
              } else {
                find.quantity = data.newQuantity;
              }
            }
          });
      }
    },

    // очистить корзину полностью на странице корзины
    clearCart() {
      this.$parent.$parent.deleteJson('/api/cart/clearCart')
        .then((data) => {
          if (data.result === 1) {
            this.cartItems.splice(0, this.cartItems.length);
          }
        });
      window.scrollBy(0, -100000);
    },

    // кол-во товара по его id
    getProductQuantity(product) {
      const find = this.cartItems.find((el) => el.id_product === product.id_product);
      if (!find) return 0;
      return find.quantity;
    },

    // subTotal price для одного товара на странице корзины
    calcSubtotalPrice(item) {
      const find = this.cartItems.find((el) => el.id_product === item.id_product);
      if (!find) return 0;
      return find.quantity * find.price;
    },
  },


  computed: {
    // итоговая цена корзины
    totalPrice() {
      /* eslint max-len: ["error", { "code": 120 }] */
      return this.cartItems.reduce((totalPrice, product) => totalPrice + product.price * product.quantity, 0);
    },

    // всего вещей в корзине (для иконки корзиныв верху страниц, красный кружок с цифрой)
    goodsQuantity() {
      /* eslint max-len: ["error", { "code": 120 }] */
      return this.cartItems.reduce((goodsQuantity, product) => goodsQuantity + (+product.quantity), 0);
    },
  },

  mounted() {
    this.$parent.$parent.getJson('/api/cart')
      .then((data) => {
        for (let i = 0; i < data.contents.length; i += 1) {
          this.cartItems.push(data.contents[i]);
        }
      });
  },

};
</script>
