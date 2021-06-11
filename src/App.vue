<template>
  <div id="nav">
    <router-link to="/">Home</router-link> |
    <router-link to="/about">About</router-link>
  </div>
  <router-view/>
  <h1>Welcome {{ displayName }} [{{ userId }}] !</h1>
  <img alt=pictureUrl src=require(pictureUrl)>
</template>

<script>
import liff from '@line/liff';

export default {
  data() {
    return {
      isLoggedIn: true,
      displayName: 'unknown',
      userId: 'unknown',
      pictureUrl: 'unknown',
    }
  },

  mounted() {
    console.log('liff init...');
    
    liff
      .init({ liffId: '1656094959-d5AOBmLz' })
      .then(() => {
          console.log('liff init success.');
          this.isLoggedIn = liff.isLoggedIn();

          if(!liff.isLoggedIn()) {
            console.log('liff requires to login.');
            liff.login();
          }

          console.log('getProfile...');
          liff
            .getProfile()
            .then(profile => {
              console.log('getProfile success.');
              this.displayName = profile.displayName;
              this.userId = profile.userId;
              this.pictureUrl = profile.pictureUrl;
            })
            .catch((err) => {
              console.log('getProfile error', err)
            })
      })
      .catch((err) => {
        console.log('liff init error.', err);
      })
      .finally(() => {
        console.log('liff init finally end');
      });
  }
}
</script>

<style>
@import './assets/styles.css';
</style>
