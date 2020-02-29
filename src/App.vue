<template>
  <div id="app">
    <header-section ref="header"></header-section>
    <top-navigation></top-navigation>
    <router-view ref="dynamic"></router-view>
    <feedback-section></feedback-section>
    <bottom-section></bottom-section>
    <footer-section></footer-section>
    <message ref="message"></message>
    <error ref="error"></error>
  </div>
</template>

<script>
import HeaderSection from '@/components/blocks/HeaderSection.vue';
import TopNavigation from '@/components/blocks/TopNavigation.vue';
import FeedbackSection from '@/components/blocks/FeedbackSection.vue';
import BottomSection from '@/components/blocks/BottomSection.vue';
import FooterSection from '@/components/blocks/FooterSection.vue';
import Message from '@/components/blocks/Message.vue';
import Error from '@/components/blocks/Error.vue';

export default {

  components: {
    HeaderSection,
    TopNavigation,
    FeedbackSection,
    BottomSection,
    FooterSection,
    Message,
    Error,
  },

  methods: {
    getJson(url) {
      return fetch(url)
        .then((result) => result.json())
        .catch((error) => {
          this.$refs.error.setError(error);
        });
    },
    postJson(url, data) {
      return fetch(url, {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json',
        },
        body: JSON.stringify(data),
      }).then((result) => result.json())
        .catch((error) => {
          this.$refs.error.setError(error);
        });
    },
    putJson(url, data) {
      return fetch(url, {
        method: 'PUT',
        headers: {
          'Content-Type': 'application/json',
        },
        body: JSON.stringify(data),
      }).then((result) => result.json())
        .catch((error) => {
          this.$refs.error.setError(error);
        });
    },
    deleteJson(url) {
      return fetch(url, {
        method: 'DELETE',
        headers: {
          'Content-Type': 'application/json',
        },
      }).then((result) => result.json())
        .catch((error) => {
          this.$refs.error.setError(error);
        });
    },
  },

  // при переходе по страницам отматывать страницу к верху
  watch: {
    $route(to, from) {
      if (from.name) {
        window.scrollBy(0, -1000000);
      }
    },
  },
};
</script>
