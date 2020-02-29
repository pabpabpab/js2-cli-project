<template>
  <p class="sortBy_input_wrapper">
    <!-- eslint-disable max-len -->
    <select name="sortBy"
            class="sortBy_input"
            v-model="userProductQuantityPerPage"
            @change="$parent.$refs.products.divideProductsIntoPages(userProductQuantityPerPage)">
      <option v-for="n in 15" :value="n" :key="n">{{ getOptionText(n) }}</option>
    </select>
  </p>
</template>

<script>
export default {
  name: 'ProductQuantityPerPage',
  data() {
    return {
      userProductQuantityPerPage: 9,
    };
  },

  methods: {
    getOptionText(n) {
      return (n < 10) ? `0${n}` : n;
    },
  },
};
</script>
