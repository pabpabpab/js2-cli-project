<template>
  <div class="gratitudes">
    <div class="one_gratitude">
      <div class="one_gratitude_left">
        <a href="#" @click.prevent>
          <img :src="imgPath + feedbackList[currentIndex].img"
               alt="person" class="grateful_person_img">
        </a>
      </div>
      <div class="one_gratitude_right">
        <p class="gratitude_text">{{feedbackList[currentIndex].text}}</p>
        <a href="#" class="person_name" @click.prevent>{{feedbackList[currentIndex].name}}</a>
        <p class="person_city">{{feedbackList[currentIndex].city}}</p>
        <div class="gratitudes_switcher">
          <a href="#"
             v-for="(item, index) of feedbackList"
             :key="item.id" :class="feedbackList[index].link_css"
             @click.prevent="getFeedback(index)"></a>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'Feedback',
  data() {
    return {
      // начальное значение, иначе предупреждения в консоль при загрузке
      feedbackList: [
        {
          id: 1, text: '', name: '', img: 'bin_burhan.png', city: '',
        },
      ],
      currentIndex: 0,
      activeCss: 'gratitudes_switcher_link gratitudes_switcher_link_active',
      inactiveCss: 'gratitudes_switcher_link',
      imgPath: 'http://localhost:8080/img/',
    };
  },

  methods: {

    resetLinkCss() {
      for (let i = 0; i < this.feedbackList.length; i += 1) {
        this.feedbackList[i].link_css = this.inactiveCss;
      }
    },

    getFeedback(index) {
      this.currentIndex = index;
      this.resetLinkCss();
      this.feedbackList[index].link_css = this.activeCss;
    },
  },

  mounted() {
    this.$parent.$parent.getJson('/api/feedback-list/')
      .then((data) => {
        // обнулить массив с начальными значениями
        this.feedbackList.splice(0, this.feedbackList.length);
        // заполнить массив отзывами, и еще добавить css
        for (let i = 0; i < data.length; i += 1) {
          this.feedbackList.push({ ...data[i], ...{ link_css: this.inactiveCss } });
        }
        // установить первую ссылку активной
        this.feedbackList[0].link_css = this.activeCss;
      });
  },
};
</script>
