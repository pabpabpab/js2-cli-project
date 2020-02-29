<template>
  <select name="sortBy"
          class="sortBy_input"
          v-model="userSort"
          @change="$parent.$refs.products.sort(userSort)">
    <option disabled value=""></option>
    <option>Name</option>
    <option>Price</option>
    <option>Size</option>
  </select>
</template>

<script>
export default {
  name: 'Sort',
  data() {
    return {
      userSort: '',
    };
  },
};
</script>
