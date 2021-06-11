<template>
  <div id="nav">
    <router-link to="/">Home</router-link> |
    <router-link to="/about">About</router-link>
  </div>
  <router-view/>
  <h1>isLoggedIn is {{ isLoggedIn }}</h1>
  <h1>Welcome {{ userName }} !</h1>
</template>

<style>
@import './assets/styles.css';
</style>

<script>
import liff from '@line/liff';

export default {
  data() {
    return {
      isLoggedIn: true,
      userName: 'unknown'
    }
  },

  mounted() {
    console.log('mounted start');
    console.log(this.isLoggedIn);
    
    liff
      .init({ liffId: '1656094959-d5AOBmLz' })
      .then(() => {
          console.log('init success');
          this.isLoggedIn = liff.isLoggedIn();
          console.log(this.isLoggedIn);

          if(!liff.isLoggedIn()) {
            liff.login();
          }

          liff
            .getProfile()
            .then(profile => {
              this.userName = profile.displayName
              console.log(this.userName);
            })
            .catch((err) => {
              console.log('getProfile error', err)
            })
          
          const idToken = liff.getDecodedIDToken();
          console.log(idToken); // print decoded idToken object
      })
      .catch((err) => {
        console.log('init failed', err);
      });

    console.log('mounted end');
  }
}
</script>
