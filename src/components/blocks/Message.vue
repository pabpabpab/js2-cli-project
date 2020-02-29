<template>
  <div class="message-wrapper" v-if="isVisible" @click="setMessage('')">
    <div class="message-block" v-html="text"></div>
  </div>
</template>

<script>
export default {
  name: 'Message',
  data() {
    return {
      text: '',
    };
  },
  methods: {
    setMessage(message) {
      this.text = message;
    },
  },
  computed: {
    isVisible() {
      return this.text !== '';
    },
  },
};
</script>
