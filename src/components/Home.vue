<template>
  <div id="home">
    <h1>{{msg}}</h1>
    <!-- @click="メソッド名"の形でクリックすると実行する命令を記載 -->
    <button @click="googleLogin">Googleアカウントでログイン</button>
  </div>
</template>

<script>
  export default {
    name: 'home',
    data () {
      return {
        msg: 'Welcome to MyMarkdown'
      }
    },
    // template内で呼び出すためのメソッドをmethods:{}の形式で定義する
    methods: {
      googleLogin: function() {
        firebase.auth().signInWithRedirect(new firebase.auth.GoogleAuthProvider());
      }
    }
  }
</script>
