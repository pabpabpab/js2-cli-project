<template>
  <nav class="catalog_bottom_nav" v-if="customized.length > 1">
    <div class="left_pagination">
      <a href="#" class="pagination_link"
         @click.prevent="productsAPI.showPage(currentPage - 1)">
        <i class="fas fa-angle-left"></i>
      </a>

      <a href="#" class="pagination_link pdl0 pdr0"
         @click.prevent="productsAPI.showPage(0)"
         v-if="((customized.length > (wing * 3)) && (currentPage > (wing + 1)))">
        1 ......
      </a>

      <a href="#"
         :class="productsAPI.paginationLinkCssArr[item - 1]"
         @click.prevent="productsAPI.showPage(item - 1)"
         v-for="item of productsAPI.paginationLinksShot" :key="item">
        {{ item }}
      </a>

      <!-- eslint-disable max-len -->
      <a href="#" class="pagination_link pdl0 pdr0"
         @click.prevent="productsAPI.showPage(customized.length - 1)"
         v-if="(customized.length > (wing * 3)) && ((currentPage + (wing + 2)) < customized.length)">
        ...... {{customized.length}}
      </a>

      <a href="#" class="pagination_link"
         @click.prevent="productsAPI.showPage(currentPage + 1)">
        <i class="fas fa-angle-right"></i>
      </a>
    </div>
    <div class="right_viewAll">
      <a href="#" class="viewAll_link"
         @click.prevent="productsAPI.divideProductsIntoPages(1000000);">
        View All
      </a>
    </div>
  </nav>
</template>

<script>
export default {
  name: 'Pagination',
  data() {
    return {
      productsAPI: this.$parent.$refs.products,
      wing: 0, // половинка длины активного кадра ссылок пагинации
    };
  },

  computed: {
    currentPage() {
      return this.productsAPI.currentPage;
    },
    customized() {
      return this.productsAPI.customized;
    },
  },

  mounted() {
    this.wing = this.productsAPI.half_length_of_pagination_shot;
  },
};
</script>
