<template>
    <ul class="list">
      <li v-for="(urls, index) in url" :key="urls" @click="selectedIssue(urls)">
        <em v-if="urls.locked === true">Closed</em>
        <em v-else>Open</em>
        
        <strong>{{ urls.title }}</strong>

        <span v-for="labels in urls.labels" :style="{backgroundColor: '#' + labels.color}" :key="labels">
          {{ labels.name }}
        </span>

        <small>
          #{{ urls.number }} opened {{ getDate(urls.created_at) }} by {{ urls.user.login }}
        </small>
      </li>

      <li v-if="appearDetailIssue" class="list__details">
        <h4 class="title">Details of Issue {{this.showIssue.number}}</h4>

        <p>
          <strong>{{this.showIssue.title}}</strong> <br><br>
          {{this.showIssue.body}} <br><br>
        </p>

        <h3 @click="appearEditIssue">{{ editIcon }} Edit a New Issue</h3> <br>

        <form v-if="editIssueForm" v-on:submit.prevent="editIssue" class="create__form">
          <div class="create__form-collum">
            <label for="Title">Title</label>
            <input v-model="issueE.name" name="Title" type="text">
          </div>

          <div class="create__form-collum full">
            <label for="Description">Description</label>
            <textarea v-model="issueE.description" name="Description"></textarea>
          </div>

          <input type="hidden" v-model="this.showIssue.number" />
          <button>Edit Issue</button>
        </form>

        <button v-if="this.showIssue.locked === true" @click="unlockIssue" class="btn-unlock">Unlock Issue</button>

        <div v-else>
          <label for="">Encerrar?</label> <br><br>

          <div class="create__form-collum">
            <label for="">Motivo</label>
            <select v-if="this.showIssue.locked === false" v-model="issueE.lockReason">
              <option>off-topic</option>
              <option>too heated</option>
              <option>spam</option>
              <option>resolved</option>
            </select>
          </div>

          <div class="create__form-collum">
            <br>
            <button @click="lockIssue" class="btn-lock">Lock Issue</button>
          </div>
        </div>
      </li>
    </ul>
</template>

<script>
import moment from 'moment'

const urlAPI = 'https://api.github.com/repos/giovanapereira/github-issues-gc/issues';
const headersAPI = { "Content-Type": "application/json; charset=utf-8", "Authorization": "token 1adb4ef21ff9b95afdfe26e73f3c1fb287bc2dea" };

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
        description: "",
        lockReason: ""
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
    getDate : function (date) {
      return moment(date, 'YYYY-MM-DD').format('DD/MM/YYYY');
    },
    selectedIssue(issue) {
      this.showIssue = issue;

      this.issueE = {
        number: issue.number,
        name: issue.title,
        description: issue.body
      };
      
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
      fetch(urlAPI + '/' + this.showIssue.number, {
        headers: headersAPI,
        method: 'PATCH',
        body: JSON.stringify({
          title: this.issueE.name,
          body: this.issueE.description
        })
      })
      .then((res)=> console.log(res.body))
      .catch((error)=> console.log(error))
    },
    lockIssue() {

      fetch(urlAPI + '/' + this.showIssue.number +'/lock', {
        headers: headersAPI,
        method: 'PUT',
        body: JSON.stringify({
          locked: true,
          active_lock_reason: this.issueE.lockReason
        })
      })
      .then((res)=> console.log(res.body))
      .catch((error)=> console.log(error))
    },
    unlockIssue() {
      fetch(urlAPI + '/' + this.showIssue.number +'/lock', {
        headers: headersAPI,
        method: 'DELETE'
      })
      .then((res)=> console.log(res.body))
      .catch((error)=> console.log(error))
    }
  }
}
</script>
