<template>
    <div id="create">
        <form v-on:submit.prevent="newIssue">
            <input v-model="issue.name" type="text">
            <input v-model="issue.label" type="text">
            <textarea v-model="issue.description"></textarea>

            <button>New Issue</button>
        </form>
    </div>
</template>

<script>
export default {
    name: "MeowCreateIssue",
    data() {
        return {
            issue: {
                name: "",
                label: "",
                description: ""
            }
        }
    },
    created() {
        
    },
    methods: {
        newIssue: function () {
            fetch('https://api.github.com/repos/giovanapereira/github-issues-gc/issues', {
                headers: { "Content-Type": "application/json; charset=utf-8", "Authorization": "token ec8d2e458e70d4dd3830f4d611d7ae544a017714" },
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

            


