<!-- templateはHTMLを記載する場所 -->
<template>
  <div id="app">
    <!-- v-if="isLogin"は条件付きレンダリング(コンポーネントを表示するかどうかを中に入れた条件に応じて決める) -->
    <Home v-if="!isLogin"></Home>
    <Editor v-if="isLogin" :user="userData"></Editor>
  </div>
</template>

<script>
// 相対パスでvueファイルをimportして読み込み
import Home from './components/Home.vue';
import Editor from './components/Editor.vue';

export default {
  name: 'app',
  data () {
    return {
      isLogin: false,
      userData: null,
    }
  },
  created: function() {
    firebase.auth().onAuthStateChanged(user => {
      console.log(user);
      if (user) {
        this.isLogin = true;
        this.userData = user;
      } else {
        this.isLogin = false;
        this.userData = null;
      };
    });
  },
  components: {
    // {tag名: 読み込んだvueファイル}の形式でtemplate内で利用したいコンポーネントを定義
    'Home': Home,
    'Editor': Editor,
  },
}
</script>
