<template>
  <div id="nav">
    <router-link to="/">Home</router-link> |
    <router-link to="/about">About</router-link>
  </div>
  <router-view/>
  <h1>logedIn is {{ loginStatus }}</h1>
</template>

<style>
@import "./assets/styles.css";
</style>

<script>
import liff from '@line/liff';

export default {
  loggedIn : true,

  props: {
    loginStatus: Boolean
  },

  created () {
    liff.init({
      liffId: '1656094959-d5AOBmLz'
    })
    .then(() => {
      this.loggedIn = liff.isLoggedIn();
    })
  },
}
</script>
