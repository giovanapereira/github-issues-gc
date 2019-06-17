<template>
    <div class="create">
        <h3 @click="appearCreateIssue">{{ createIcon }} Create a New Issue</h3>
        
        <form v-if="createIssue" v-on:submit.prevent="newIssue" class="create__form">
            <div class="create__form-collum">
                <label for="Title">Title</label>
                <input v-model="issue.name" name="Title" type="text" placeholder="Type here your title...">
            </div>

            <div class="create__form-collum">
                <label for="Label">Label</label>
                <input v-model="issue.label" name="Label" type="text" placeholder="Type here your label...">
            </div>

            <div class="create__form-collum full">
                <label for="Description">Description</label>
                <textarea v-model="issue.description" name="Description"></textarea>
            </div>

            <button>New Issue</button>
        </form>
    </div>
</template>

<script>
const urlAPI = 'https://api.github.com/repos/giovanapereira/github-issues-gc/issues';
const headersAPI = '"Content-Type": "application/json; charset=utf-8", "Authorization": "token  3e95cd3edd37b166d3d4c1f92d1e77757c54af18"';

export default {
    name: "MeowCreateIssue",
    data() {
        return {
            createIssue: false,
            createIcon: '+',
            issue: {
                name: "",
                label: "",
                description: ""
            }
        }
    },
    methods: {
        appearCreateIssue: function () {
            this.createIcon = '+'
            this.createIssue = !this.createIssue

            if (this.createIssue === true) {
                this.createIcon = '-'
            }
        },
        newIssue: function () {
            fetch(urlAPI, {
                headers: { headersAPI },
                method: 'POST',
                body: JSON.stringify({
                    title: this.issue.name,
                    body: this.issue.description,
                    labels: [
                        this.issue.label
                    ]
                })
            })
            .then((res)=> console.log(res.body))
            .catch((error)=> console.log(error))
        }
    }
}
</script>

            


