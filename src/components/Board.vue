<template>
  <div>
    <top-bar
      :current-date-time="currentDateTime"
      @start-timer="startTimer" />
    <div style="display: flex">
      <!--  5. dodaj html -->
      <navigation
        :course="[currentCourse]"
        @select-content="changeCurrentContent" />
      <div>
        <div v-html="current.content"/>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios';
import Navigation from './Navigation.vue';
import TopBar from './TopBar.vue';


export default {
  name: 'Board',
  components: {
    TopBar,
    Navigation,
    
  },
  data() {
    return {
      courses: [],
      currentCourse: {},
      currentCourseTopicId: null,
      currentCourseQuestId: null,
      currentDateTime: '',
      showTimer: true
    };
  },
  props: {
    refreshRate: { type: Number, default: 35000 },
    courseLearnEndpoint: { type: String, default: '' },
  },
  async created() {
    await this.fetch();
    this.updateDateTime(); // Dodajemy wywołanie metody do aktualizacji daty i godziny
  },
  computed: {
    endpoint() {
      return `${this.courseLearnEndpoint}`;
    },
    current() {
      const current = this.currentCourse && this.currentCourseTopicId
        ? (!this.currentCourseQuestId
          ? this.courses[0].topics.find(item => item.id === this.currentCourseTopicId)
          : this.courses[0].topics.find(item => item.id === this.currentCourseTopicId).child
            .find(item => item.id === this.currentCourseQuestId))
        : {};
      
      const result = current
        ? current
        : (() => {
          this.currentCourseTopicId = this.courses[0].topics[0].id;
          this.currentCourseQuestId = null;
          return this.courses[0].topics.find(item => item.id === this.currentCourseTopicId);
        })();
      
      return result;
    },
  },
  methods: {
    async fetch() {
      try {
        const coursesResponse = this.courseLearnEndpoint
          ? await axios.get(this.endpoint)
          : {};
        if (Array.isArray(coursesResponse.data)
          && coursesResponse.data && coursesResponse.data.length > 0) {
          const coursesFormatted = this.format(coursesResponse.data);
          if (JSON.stringify(this.courses) !== JSON.stringify(coursesFormatted)) {
            this.courses = coursesFormatted;
            this.currentCourse = this.courses[0];
            this.currentCourseTopicId = !this.currentCourseTopicId
              ? this.courses[0].topics[0].id
              : this.currentCourseTopicId;
          }
        }
      } catch (ex) {
        this.courses = [];
      }
    },
    format(response) {
      return JSON.parse(JSON.stringify(response).replace(/"quests":/g, '"child":'));
    },
    changeCurrentContent(item) {
      console.log(item);
      if (item.content) {
        if (this.current.content !== item.content) {
          this.$root.$emit('reset-view');
          window.scrollTo(0, 0);
        }
        if (item.child) {
          this.currentCourseTopicId = item.id;
          this.currentCourseQuestId = null;
        } else {
          this.currentCourseTopicId = item.topic_id;
          this.currentCourseQuestId = item.id;
        }
      }
    },
    updateDateTime() {
      setInterval(() => {
        const now = new Date();
        const options = {
          weekday: 'long',
          year: 'numeric',
          month: 'long',
          day: 'numeric',
          hour: 'numeric',
          minute: 'numeric',
          second: 'numeric',
        };
        this.currentDateTime = now.toLocaleDateString(undefined, options);
      }, 1000); // Aktualizacja co sekundę
    },
  },
};
</script>