<template>
     <div class="rss">
    <h1 class="tit">Create AIML Tools</h1>
    <p class="txt">docomoタグが付いた投稿をQiitaAPIv2を用いて取得し、最新の5件を表示。</p>
    <div class="tag_searcher">
      <input type="text" v-model="tag" name="" value="">
      <input type="button" v-on:click="getUrl()" name="" value="search">
      <input type="button" v-on:click="postUrl()" name="" value="send">
    </div>
    <div class="qiita_wrap">
      <!-- v-forで最新の5件を取得して装飾してます -->
      <div v-for='(post, index) in 5' :key='index' class="qiita">
        <ul>
          <li>
            <a :href="posts[index].url" target="_blank">
              <div class="qiita_content">
                <figure><img :src="posts[index].user.profile_image_url" alt=""></figure>
                <p class="user_name" v-if="posts[index].user.name">{{posts[index].user.name}}</p><p class="time"> posted at {{posts[index].updated_at.slice(0,-15)}}</p>
              </div>
              <p class="qiita_txt">{{posts[index].title.slice(0,48)}}<span v-if="posts[index].title.length > 48" >...</span></p>
            </a>
          </li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios'
export default {
  data () {
    return {
      posts: [],
      url: '',
      tag: ''
    }
  },
  methods: {
    getApi () {
      // axiosでqiitaから記事の情報を取得してpostsに代入
      axios
        .get(`https://qiita.com/api/v2/tags/docomo/items`)
        .then(response => (this.posts = response.data))
    },
    // タグ検索メソッド
    getUrl () {
      this.url = `https://qiita.com/api/v2/tags/${this.tag}/items`
      axios
        .get(`${this.url}`)
        .then(response => (this.posts = response.data))
    },
    // WebAPIにpostしてデータを取得する
    postUrl () {
      this.url = `http://weather.livedoor.com/forecast/webservice/json/v1?city=400040`
      axios
        .post(`${this.url}`)
        .then(response => console.log(response))
    }
  },
  // ページが読み込まれた際に実行したい{処理}
  created () {
    this.getApi()
  }
}
</script>
