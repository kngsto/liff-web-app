<template>
  <router-view/>
  <h1 class="match-text animation-fadein">{{ displayName }}さんにマッチしたMeowがいます！</h1>
  <div class="match-picture">
    <img class="meow-picture animation-fadein" alt=pictureUrl :src=pictureUrl>
    <img class="animation-fadein" src="./assets/heart.svg" width="32" height="32">
    <img id="meow-img" class="meow-picture animation-fadein" alt=meowUrl :src=meowUrl>
  </div>
  <div class="like-button animation-fadein">
    <img class="button-icon" src="./assets/like.svg" width="64" height="64" @click="searchMeow">
  </div>
</template>

<script>
import liff from '@line/liff';
import axios from 'axios'

export default {
  data() {
    return {
      isLoggedIn: true,

      // ユーザ情報
      displayName: 'unknown',
      userId: 'unknown',
      pictureUrl: 'unknown',

      // マッチしたMeow情報
      meowId: 'unknown',
      meowUrl: 'unknown',
    }
  },

  methods: {
    // TheCatApiを使ってランダムな猫の画像URLを取得する
    searchMeow() {
      console.log('searchMeow')

      axios
        .get('https://api.thecatapi.com/v1/images/search')
        .then((res) => {
          console.log(res);
          this.meowId = res.data[0]['id'];
          this.meowUrl = res.data[0]['url'];
          console.log(this.meowId);
          console.log(this.meowUrl);

          // Meow写真のアニメーション再描画
          document.getElementById('meow-img').className = 'meow-picture';
          window.requestAnimationFrame(function() {
            window.requestAnimationFrame(function() {
              document.getElementById('meow-img').className = 'meow-picture animation-fadein';
            });
          });
        });
    }
  },

  mounted() {
    const debugMode = process.env.VUE_APP_DEBUG_MODE;

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

    // マッチしたMeowを表示する
    this.searchMeow();
  }
}
</script>

<style>
@import './assets/styles.css';

.match-text {
  text-align: center;
  padding: 20px;
}

.match-picture {
  text-align: center;
}

.meow-picture {
  width: 110px;
  height: 110px;
  border-radius: 50%;
  background-position: center center;
  background-size: cover;
  display: inline-block;
  vertical-align: middle;
  margin: 20px;
}
.animation-fadein {
  opacity: 0;
  animation: 3s fadeIn forwards;
  animation-delay: 1s;
}
@keyframes fadeIn {
  from { opacity: 0; }
  to { opacity: 1; }
}

.like-button {
  margin: 60px;
  text-align: center;
}
</style>
