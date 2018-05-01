<template>
  <div id="editor">
    <h1>エディター画面</h1>
    <span>{{user.displayName}}</span>
    <button @click="logout">ログアウト</button>
    <div class="memoListWrapper">
      <!-- v-forで配列やオブジェクトに応じた表示を行う(リストレンダリング) -->
      <div class="memoList" v-for="(memo, index) in memos" @click="selectMemo(index)" data-selected="index==selectedIndex">
        <p class="memoTitle">{{displayTitle(memo.markdown)}}</p>
      </div>
      <button class="addMemoBtn" @click="addMemo">メモの追加</button>
      <button class="deleteMemoBtn" v-if="memos.length > 1" @click="deleteMemo">選択中のメモを削除</button>
      <button class="saveMemosBtn" @click="saveMemos">メモの保存</button>
    </div>
      <!-- v-modelはinputやtextareaの状態をコンポーネントのデータへ格納する
            textareaに書き込んだテキストが自動的にv-modelへ指定した変数へ格納される(データバインディング)-->
      <textarea class="markdown" v-model="memos[selectedIndex].markdown"></textarea>
      <!--  v-htmlは指定された値を直接HTMLとして描画する-->
      <div class="preview" v-html="preview()"></div>
    </div>
  </div>
</template>

<script>
  import marked from 'marked';
  export default {
    name: 'editor',
    props: ['user'],
    data () {
      return {
        memos:[{markdown: ''
       }],
       selectedIndex: 0
        }
      },
      created: function() {
        firebase
          .database()
          .ref('memos/' + this.user.uid)
          .once('value')
          .then(result => {
            if(result.val()) {
              this.memos = result.val();
            }
          })
      },
      methods: {
        logout: function() {
          firebase.auth().signOut();
        },
        addMemo: function(){
          this.memos.push({
            markdown: '無題のメモ',
          })
        },
        selectMemo: function(index) {
          this.selectedIndex = index;
        },
        preview: function() {
          return marked(this.memos[this.selectedIndex].markdown);
        },
        displayTitle: function(text) {
          return text.split(/\n/)[0];
        },
        deleteMemo: function() {
          this.memos.splice(this.selectedIndex, 1);
          if (this.selectedIndex > 0) {
            this.selectedIndex--;
          }
        },
        saveMemos: function() {
          firebase
            .database()
            .ref('memos/' + this.user.uid)
            .set(this.memos);
        },
      }
    }
</script>

<style lang="scss" scoped>
// scopedを記述することでコンポーネント内でしか適用されなくなる
  .memoListWrapper {
    width: 19%;
    float: left;
    border-top: 1px solid #000;
  }
  .memoList {
    padding: 10px;
    box-sizing: border-box;
    text-align: left;
    border-bottom: 1px solid #000;
    &:nth-child(even){
      background-color: #ccc;
    }
    &[data-selected="true"]{
      background-color: #ccf;
    }
  }
  .memoTitle {
    height: 1.5em;
    margin: 0;
    white-space: nowrap;
    overflow: hidden;
  }
  .addMemoBtn {
    margin-top: 20px;
  }
  .deleteMemoBtn {
    margin: 10%;
  }
  .markdown {
    float: left;
    width: 40%;
    height: 500px;
  }
  .preview {
    float: left;
    width: 40%;
    text-align: left;
  }
</style>
