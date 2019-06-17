<template>
    <ul class="list">
      <li v-for="(urls, index) in url" :key="urls" @click="selectedIssue(urls)">
        <em v-if="urls.locked === true">Open</em>
        <em v-else>Closed</em>
        
        <strong>{{ urls.title }}</strong>

        <span v-for="labels in urls.labels" :style="{backgroundColor: '#' + labels.color}" :key="labels">
          {{ labels.name }}
        </span>

        <small>
          #{{ urls.number }} opened {{ urls.created_at | moment("from", "now") }} by {{ urls.user.login }}
        </small>
      </li>

      <li v-if="appearDetailIssue" class="list__details">
        <h4 class="title">Details of Issue {{this.showIssue.number}}</h4>

        <p>
          <strong>{{this.showIssue.title}}</strong> <br><br>
          {{this.showIssue.body}} <br><br>
        </p>

        <h3 @click="appearEditIssue">{{ editIcon }} Edit a New Issue</h3> <br>

        <form v-if="editIssueForm">
          <label for="Title">Title</label>
          <input v-model="issueE.name" name="Title" type="text" placeholder="Type here your title...">

          <label for="Labels">Labels</label>
          <input v-model="issueE.label.name" name="Label" type="text" placeholder="Type here your label...">

          <label for="Description">Description</label>
          <textarea v-model="issueE.description" name="Description"></textarea>

          <input type="hidden" v-model="this.showIssue.number" />

          <button v-if="this.showIssue.locked === true" @click="unlockIssue" class="btn-unlock">Unlock Issue</button>

          <button v-else @click="lockIssue" class="btn-lock">Lock Issue</button>

          <button @click="editIssue">Edit Issue</button>
        </form>
      </li>

    </ul>
</template>

<script>
const urlAPI = 'https://api.github.com/repos/giovanapereira/github-issues-gc/issues';
const headersAPI = '"Content-Type": "application/json; charset=utf-8", "Authorization": "token  3e95cd3edd37b166d3d4c1f92d1e77757c54af18"';

export default {
  name: 'MeowIssue',
  data () {
    return {
      url: [],
      showIssue: {},
      appearDetailIssue: false,
      editIssueForm: false,
      editIcon: '+',
      issueE: {
        number: "",
        name: "",
        label: "",
        description: ""
      }
    }
  },
  created() {
    fetch(urlAPI)
    .then(response => response.json())
    .then(json => {
      this.url = json
    })
  },
  methods: {
    selectedIssue(issue) {
      this.showIssue = issue;
      this.appearDetailIssue = !this.appearDetailIssue
    },
    appearEditIssue: function () {
      this.editIcon = '+'
      this.editIssueForm = !this.editIssueForm

      if (this.editIssueForm === true) {
          this.editIcon = '-'
      }
    },
    editIssue() {
      const number = this.showIssue.number;
      
      fetch(urlAPI + number, {
        headers: { headersAPI },
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

      fetch(urlAPI + number +'/lock', {
        headers: { headersAPI },
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

      fetch(urlAPI + number +'/lock', {
        headers: { headersAPI },
        method: 'DELETE'
      })
      .then((res)=> console.log(res.body))
      .catch((error)=> console.log(error))
    }
  }
}
</script>
