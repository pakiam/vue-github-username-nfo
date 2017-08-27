<template>
    <div>
        <p v-if="currentUsername == null">
            Enter a username above to see their Github data
        </p>
        <p v-else>
            Below are the results for {{ currentUsername }}
            </p>
        <form v-on:submit.prevent="onSubmit">
            <input type="text" v-model="currentUsername" placeholder="Enter a github username here">
            <button type="submit">Go!</button>
        </form>
        <github-user-data :data="githubData[currentUsername]"></github-user-data>
    </div>
</template>


<script>
  import Vue from 'vue'
  import bus from '../bus'
  import GithubUserData from './GithubUserData.vue'
  export default{
    name: 'github-input',
    data () {
      return {
        currentUsername: null,
        githubData: {}
      }
    },
    components: {
      'github-user-data': GithubUserData
    },
    created () {
      bus.$on('new-username', this.onUsernameChange)
    },
    destroyed () {
      bus.$off('new-username', this.onUsernameChange)
    },
    methods: {
      onSubmit (event) {
        if (this.currentUsername && this.currentUsername !== '') {
          bus.$emit('new-username', this.currentUsername)
        }
      },
      onUsernameChange (name) {
        this.currentUsername = name
        this.fetchGithubData(name)
      },
      fetchGithubData (name) {
        // if we have data already, dont request again
        if (this.githubData.hasOwnProperty(name)) return

        const url = `https://api.github.com/users/${name}`
        fetch(url)
          .then(r => r.json())
          .then(data => {
            Vue.set(this.githubData, name, data)
          })
      }
    }
  }
</script>

<style>


</style>