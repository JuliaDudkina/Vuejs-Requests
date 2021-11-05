<template>
  <section>
    <base-card>
      <h2>Submitted Experiences</h2>
      <div>
        <base-button @click='loadExperiences'>Load Submitted Experiences</base-button>
      </div>
      <p v-if='isLoading'>Loading...</p>
      <p v-else-if='!isLoading && error'>{{ error }}</p>
      <p v-else-if='!isLoading && (!results || results.length == 0 )'>
      No stored experience found!</p>
      <ul v-else>
        <survey-result
          v-for="result in results"
          :key="result.id"
          :name="result.name"
          :rating="result.rating"
        ></survey-result>
      </ul>
    </base-card>
  </section>
</template>

<script>
import SurveyResult from './SurveyResult.vue';

export default {
  components: {
    SurveyResult,
  },
  data(){
    return{
      results: [],
      isLoading: false,
      error: null,
    }
  },
  methods:{
    loadExperiences(){
      this.isLoading = true;
      fetch('https://vue-htttp-demo-7049c-default-rtdb.firebaseio.com/surveys')
        .then((response) => {
          if(response.ok){
            return response.json();
          }
      }).then((data) => { // arrow functions do not have their own context
        this.isLoading = false;
        const results = [];
        for (const id in data) {
            results.push({
              id: id,
              name: data[id].name,
              rating: data[id].rating
            });
        }
        this.results = results; // therefore this will refer to general context
        // if we use function keyword, we'll get an error because 'this' will have local context and not refer to an array in data
      })
      .catch(() => {
        this.isLoading = false;
        this.error = 'Failed. Try later.';
      })
    },
  },
  mounted(){ // mounted hook insures that when DOM is loaded we execute loadExperiences
    this.loadExperiences();
  }
};
</script>

<style scoped>
ul {
  list-style: none;
  margin: 0;
  padding: 0;
}
</style>