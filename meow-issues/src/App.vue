<style lang="scss">
   @import './src/scss/reset.scss';
   @import './src/scss/style.scss';
</style>


<template>
  <div id="app">
    <MeowCreateIssue/>
    <MeowIssue/>
  </div>
</template>

<script>
import { urlAPI, headersAPI } from './components/Variables'
import MeowCreateIssue from './components/MeowCreateIssue'
import MeowIssue from './components/MeowIssue'

export default {
  name: 'App',
  components: {
    MeowCreateIssue,
    MeowIssue
  }
}
</script>
