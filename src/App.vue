<template>
  <router-view/>
  <h1>{{ displayName }}さんにマッチしたMeowがいます</h1>
  <div class="match-picture">
    <img alt=pictureUrl :src=pictureUrl>
  </div>
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
    const debugMode = process.env.VUE_APP_DEBUG_MODE;
    console.log(debugMode);

    // debugモードの時はLINEログインせずにユーザ名と写真はデフォルト値を使用する
    if(debugMode=='true') {
      console.log('debug mode');
      this.displayName = 'debug name';
      this.userId = 'debug-id';
      this.pictureUrl = 'https://cdn2.thecatapi.com/images/4tm.jpg';
    }

    // LINEログインする場合はプロフィールからユーザ名とアイコン写真を取得する
    else {
      console.log('liff init...');
      liff
        .init({ liffId: process.env.VUE_APP_LIFF_ID })
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
}
</script>

<style>
@import './assets/styles.css';
</style>
