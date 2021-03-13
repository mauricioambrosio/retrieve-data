<template>
  <div id="app">
    <div 
      v-if="stories.length>0"
      class="container"
    >
      <h3>Top 10 stories</h3>
      
      <div 
        class="card mb-4 border shadow"
        v-for="story in stories" 
        :key="story.id"
      >
        <div class="card-header">
          <b>{{story.title}}</b>
        </div>

        <div class="card-body">
          <p v-if="story.text" v-html="story.text"/>
          <a v-else-if="story.url" :href="story.url">{{story.url}}</a>
        
        </div>
        
        <br/>
      
      </div>
    </div>
  </div>
</template>

<script>


export default {
  name: 'App',
  data:function(){
    return {
      storyIds: [],
      stories:[]
    }
  },
  methods:{
    // fetch one item/story based on id
    fetchStory(id){
      return new Promise((resolve)=>{
        fetch(`https://hacker-news.firebaseio.com/v0/item/${id}.json?print=pretty`, {
          method: "get"
        }).then((response) => {
          return response.json()
        }).then((jsonData)=>{
          resolve(jsonData)
        })
      })
    
    }
  },
  mounted:function(){
    // fetch top story ids
    fetch("https://hacker-news.firebaseio.com/v0/topstories.json?print=pretty", {
      method: "get"
    }).then((response) => {
      return response.json()
    }).then(async (jsonData)=>{
      // consider only the top 10
      this.storyIds = jsonData.splice(0,10)
      
      // get each story based on id and add to stories
      let i;
      for(i=0; i<this.storyIds.length; i++){
        const story = await this.fetchStory(this.storyIds[i]); 
        this.stories.push(story)
      }
    })
  },
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
