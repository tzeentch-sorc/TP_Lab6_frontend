<template>
  <div class="hello md-elevation-4" >
    <div class="md-layout md-gutter">
      <div class="md-layout-item">
        <md-field>
          <label for="topic">Current topic</label>
          <md-select v-model="currentTopic" name="topic" id="topic">
            <md-option v-for="(item, index) in topicsList" :value="item.url" v-bind:key="index">{{item.url}}</md-option>
          </md-select>
        </md-field>
        </div>
      <div class="md-layout-item">
        <md-button class="md-primary md-raised" @click="switchTopic">UPDATE</md-button>
      </div>
      <div class="md-layout-item">
        <md-field>
          <label>Add Topic</label>
          <md-input v-model="addTopic"/>
        </md-field>
      </div>
      <div class="md-layout-item">
        <md-button class="md-primary md-raised" @click="addNewTopic">ADD</md-button>
      </div>
    </div>
    <md-button v-if="currentPage > 1" class=" md-raised" @click="getPostsInCurrent(--currentPage)">Previous Page</md-button>
    <md-button v-if="currentPage < pages" class="md-raised" @click="getPostsInCurrent(++currentPage)">Next Page</md-button>
    <div>
      <md-card v-for="post in posts" v-bind:key="post.feedId" class="md-raised" style="margin: 10px">
        <md-card-header>{{post.title}}</md-card-header>
        <md-divider/>
        <div v-html="post.text"/>
      </md-card>
    </div>
  </div>
</template>

<script>
import axios from 'axios';
const URL = 'http://localhost:8080';
export default {
  name: 'HelloWorld',
  data(){
    return {
      topicsList: [],
      currentTopic: null,
      addTopic:'test',
      pages: 0,
      currentPage: 1,
      posts: []
    }
  },
  methods:{
    getList(){
      axios.get(`${URL}/list`, {withCredentials: true}).then(
          response => {
            this.topicsList = response.data;
            this.currentTopic = this.topicsList[0].url;
          }
      )
    },
    addNewTopic(){
      axios.get(`${URL}/add?arg=${this.addTopic}`, {withCredentials: true}).then(
          response => {
            this.pages = response.data;
            this.topicsList.push(...this.addTopic);
          }
      )
    },
    getPostsInCurrent(page){
      axios.get(`${URL}/page?arg=${page}`, {withCredentials: true}).then(
          response => {
            this.posts = response.data;
          }
      )

    },
    switchTopic(){
      axios.get(`${URL}/switch?arg=${this.currentTopic}`, {withCredentials: true}).then(
          response => {
            this.pages = response.data;
            this.currentPage = 1;
          }
      )
        this.getPostsInCurrent(1);
    }
  },
  mounted() {
    this.getList();
    if(this.topicsList.length > 0) {
      this.getPostsInCurrent(this.currentPage)
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.hello{
  margin-top: 5%;
  margin-left: 20%;
  margin-right: 20%;
  padding: 2%;
}
</style>
