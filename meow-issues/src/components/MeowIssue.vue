<template>
    <ul>
      <li v-for="(urls, index) in url" v-bind:key="urls" @click="selectedIssue(urls)">
        {{ urls.locked }}
        <!-- <a v-bind:href="urls.url"> -->
          {{ urls.title }}
        <!-- </a> -->

        <span v-for="labels in urls.labels" v-bind:key="labels">
          <!-- <a v-bind:href="urls.url"> -->
            {{ labels.name }}
          <!-- </a> -->
        </span>

        <small>
          #{{ urls.number }} opened {{ urls.created_at }} by {{ urls.user.login }}
        </small>

        <!-- <img v-bind:src="urls.user.avatar_url"/> -->
      </li>

      <li>
        <p>
          {{this.showIssue.number}} <span>{{this.showIssue.title}} {{this.showIssue.body}}</span>
        </p>

        <form>
          <input v-model="issueE.name" type="text">
          <!-- <input v-bind:value="this.showIssue.labels.name" type="text"> -->
          <textarea v-model="issueE.description" ></textarea>
          <input type="hidden" v-model="this.showIssue.number" />

          <button @click="unlockIssue">Unlock Issue</button>

          <button @click="lockIssue">Lock Issue</button>

          <button @click="editIssue">Edit Issue</button>
        </form>
      </li>
    </ul>
</template>

<script>
export default {
  name: 'MeowIssue',
  data () {
    return {
      url: [],
      showIssue: {},
      issueE: {
        number: "",
        name: "",
        label: "",
        description: ""
      }
    }
  },
  created() {
    fetch("https://api.github.com/repos/giovanapereira/github-issues-gc/issues")
    .then(response => response.json())
    .then(json => {
      this.url = json
    })
  },
  methods: {
    selectedIssue(issue) {
      this.showIssue = issue;
    },
    editIssue() {
      const number = this.showIssue.number;
      
      fetch('https://api.github.com/repos/giovanapereira/github-issues-gc/issues/'+ number, {
        headers: { "Content-Type": "application/json; charset=utf-8", "Authorization": "token ec8d2e458e70d4dd3830f4d611d7ae544a017714" },
        method: 'PATCH',
        body: JSON.stringify({
          title: this.issueE.name,
          body: this.issueE.description,
          // labels: [
          //     this.issue.label
          // ]
        })
      })
      .then((res)=> console.log(res.body))
      .catch((error)=> console.log(error))
    },
    lockIssue() {
      const number = this.showIssue.number;

      fetch('https://api.github.com/repos/giovanapereira/github-issues-gc/issues/'+ number +'/lock', {
        headers: { "Content-Type": "application/json; charset=utf-8", "Authorization": "token ec8d2e458e70d4dd3830f4d611d7ae544a017714" },
        method: 'PUT',
        body: JSON.stringify({
          locked: true,
          active_lock_reason: "too heated"
        })
      })
      .then((res)=> console.log(res.body))
      .catch((error)=> console.log(error))
    },
    unlockIssue() {
      const number = this.showIssue.number;

      fetch('https://api.github.com/repos/giovanapereira/github-issues-gc/issues/'+ number +'/lock', {
        headers: { "Content-Type": "application/json; charset=utf-8", "Authorization": "token ec8d2e458e70d4dd3830f4d611d7ae544a017714" },
        method: 'DELETE'
      })
      .then((res)=> console.log(res.body))
      .catch((error)=> console.log(error))
    }
  }
}
</script>
