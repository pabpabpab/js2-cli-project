<template>
  <div :class="containerClass">
    <product-item
      v-for="item of customized[currentPage]"
      :key="item.id_product" :product="item" :mode="mode">
    </product-item>
  </div>
</template>

<script>
import ProductItem from '@/components/elements/ProductItem.vue';

export default {
  name: 'Products',
  components: { ProductItem },
  props: ['mode'],
  data() {
    return {
      products: [],
      filtered: [],
      // товары из filtered, но разбитые по страницам, в соответствии с productQuantityPerPage
      customized: [[], [], []],
      containerClass: 'container featured__content',
      userSizeArr: [], // установки пользователя в фильтре размеров
      userPriceLimit: 80, // установка пользователя в фильтре цены

      // установка пользователем в выпадающем списке кол-ва товаров на страницу
      productQuantityPerPage: 9,
      currentPage: 0, // текущая страница в пагинаторе
      activePaginationLinkCss: 'pagination_link active_pagination_link',
      inactivePaginationLinkCss: 'pagination_link',
      paginationLinkCssArr: ['', '', ''],
      paginationLinksShot: [1, 2, 3, 4, 5, 6], // активный кадр ссылок пагинации
      half_length_of_pagination_shot: 3, // половинка длины активного кадра ссылок пагинации
    };
  },

  methods: {
    filter(userSearch) {
      const regexp = new RegExp(userSearch, 'i');
      this.filtered = this.products.filter((el) => regexp.test(el.product_name));

      // разбить товары по страницам
      this.divideProductsIntoPages(this.productQuantityPerPage);
    },


    // фильтрация по размеру
    sizeFilter(userSizeArr) {
      this.userSizeArr = userSizeArr; // сохранить переданные данные (для составного фильтра)

      if (userSizeArr.length > 0) {
        this.filtered = this.sizeFilterCore(this.products, userSizeArr);
      } else {
        this.filtered = [...this.products];
      }

      // если фильтр цены установлен, то применить и его
      if (this.userPriceLimit > 0) {
        this.filtered = this.priceFilterCore(this.filtered, this.userPriceLimit);
      }

      // разбить товары по страницам
      this.divideProductsIntoPages(this.productQuantityPerPage);
    },


    // фильтрация по цене
    priceFilter(userPriceLimit) {
      this.userPriceLimit = userPriceLimit; // сохранить переданные данные (для составного фильтра)

      this.filtered = this.priceFilterCore(this.products, userPriceLimit);

      // если фильтр по размерам установлен, то применить и его
      if (this.userSizeArr.length > 0) {
        this.filtered = this.sizeFilterCore(this.filtered, this.userSizeArr);
      }

      // разбить товары по страницам
      this.divideProductsIntoPages(this.productQuantityPerPage);
    },

    priceFilterCore(products, priceLimit) {
      return products.filter((el) => el.price <= priceLimit);
    },
    sizeFilterCore(products, sizeArr) {
      return products.filter((el) => sizeArr.includes(el.size));
    },

    // сортировка
    sort(userSort) {
      switch (userSort) {
        case 'Name':
          this.filtered.sort((prev, next) => {
            if (prev.product_name < next.product_name) return -1;
            if (prev.product_name < next.product_name) return 1;
            return 1;
          });
          break;
        case 'Price':
          this.filtered.sort((prev, next) => prev.price - next.price);
          break;
        case 'Size':
          this.filtered.sort((prev, next) => prev.size_number - next.size_number);
          break;
        default:
          this.filtered.sort((prev, next) => prev.price - next.price);
      }

      // разбить товары по страницам
      this.divideProductsIntoPages(this.productQuantityPerPage);
    },

    // разбить товары по страницам
    divideProductsIntoPages(userProductQuantityPerPage) {
      // поменять это значение при вызове метода из другого компонента
      if (this.mode === 'catalog') {
        this.$parent.$refs.qpp.userProductQuantityPerPage = userProductQuantityPerPage;
      }
      this.productQuantityPerPage = userProductQuantityPerPage;
      this.currentPage = 0;
      // очистить массив товаров
      this.customized.splice(0, this.customized.length);
      // очистить css ссылок пагинации
      this.paginationLinkCssArr.splice(0, this.paginationLinkCssArr.length);

      // разбиваем товары по страницам в соответствии с новым this.productQuantityPerPage
      let pageCounter = 0;
      let productCounter = 0;
      for (let i = 0; i < this.filtered.length; i += 1) {
        // страница заполнена
        if (productCounter === this.productQuantityPerPage) {
          productCounter = 0;
          pageCounter += 1;
        }
        if (productCounter === 0) {
          // начинаем следующую страницу товаров
          this.customized.push([]);
          // сделать ссылку на эту страницу товаров неактивной
          this.paginationLinkCssArr.push(this.inactivePaginationLinkCss);
        }
        // пушим товар в страницу
        this.customized[pageCounter].push(this.filtered[i]);
        productCounter += 1;
      }

      // установить активную ссылку
      this.paginationLinkCssArr[this.currentPage] = this.activePaginationLinkCss;

      // сформировать активный кадр ссылок пагинации
      this.makePaginationLinksShot();
    },


    // сформировать активный кадр ссылок пагинации
    makePaginationLinksShot() {
      let border1;
      let border2;
      const wing = this.half_length_of_pagination_shot;
      this.paginationLinksShot.splice(0, this.paginationLinksShot.length); // очистить

      if (this.customized.length <= wing * 3) {
        for (let i = 1; i <= this.customized.length; i += 1) this.paginationLinksShot.push(i);
      } else if ((this.currentPage + 1 + wing + 2) > this.customized.length) {
        /* eslint max-len: ["error", { "code": 180 }] */
        border1 = ((this.currentPage + 1 - wing) > (this.customized.length - wing * 2)) ? (this.customized.length - wing * 2) : (this.currentPage + 1 - wing);
        border2 = this.customized.length;
        for (let i = border1; i <= border2; i += 1) this.paginationLinksShot.push(i);
      } else if (this.currentPage + 1 - wing - 2 < 1) {
        border1 = 1;
        /* eslint max-len: ["error", { "code": 180 }] */
        border2 = ((this.currentPage + 1 + wing) < (wing * 2)) ? (wing * 2) : (this.currentPage + 1 + wing);
        for (let i = border1; i <= border2; i += 1) this.paginationLinksShot.push(i);
      } else {
        border1 = this.currentPage + 1 - wing;
        border2 = this.currentPage + 1 + wing;
        for (let i = border1; i <= border2; i += 1) this.paginationLinksShot.push(i);
      }
    },

    // пагинация: какую страницу товара показать
    showPage(page) {
      let index;
      if (page < 0) {
        index = 0;
      } else if (page >= this.customized.length) {
        index = this.customized.length - 1;
      } else {
        index = page;
      }
      this.currentPage = index;
      this.resetPaginationLinkCss();

      // установить активный css-стиль ссылки для актуальной страницы
      this.paginationLinkCssArr.splice(index, 1, this.activePaginationLinkCss);

      // сформировать активный кадр ссылок пагинации
      this.makePaginationLinksShot();
    },

    // сбросить css ссылок пагинации
    resetPaginationLinkCss() {
      const arrLength = this.paginationLinkCssArr.length;
      this.paginationLinkCssArr.splice(0, arrLength); // очистить
      for (let i = 0; i < arrLength; i += 1) {
        this.paginationLinkCssArr.push(this.inactivePaginationLinkCss);
      }
    },

  },

  mounted() {
    let productsUrl;
    switch (this.mode) {
      case 'featured-products':
        this.containerClass = 'container featured__content';
        productsUrl = '/api/featured-products';
        break;
      case 'you-may-like-also':
        this.containerClass = 'container also_content';
        productsUrl = '/api/also-products';
        break;
      default:
        this.containerClass = 'featured__content mt10 min-width-100';
        productsUrl = '/api/products';
    }

    this.$parent.$parent.getJson(productsUrl)
      .then((data) => {
        for (let i = 0; i < data.length; i += 1) {
          this.products.push(data[i]);
          this.filtered.push(data[i]);
        }
        // разбиваем товары по страницам
        this.divideProductsIntoPages(this.productQuantityPerPage);
      });
  },

};
</script>
