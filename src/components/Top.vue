<template>
     <div class="rss">
    <h1 class="tit">Create AIML Tools</h1>
    <p class="txt">docomoタグが付いた投稿をQiitaAPIv2を用いて取得し、最新の5件を表示。</p>
    <div class="tag_searcher">
      <input type="text" v-model="tag" name="" value="">
      <input type="button" v-on:click="getUrl()" name="" value="search">
      <input type="button" v-on:click="postUrl()" name="" value="postapi">
      <input type="button" v-on:click="createAIMLTEST()" name="" value="DLtest">
    </div>
    <div>
      <p>method</p><input type="text" id="method_data" v-model="txt_header" name="" value=""><br>
      <p>url</p><input type="text" id="url_data" v-model="txt_header" name="" value=""><br>
      <p>header</p><input type="text" id="header_data" v-model="txt_header" name="" value=""><br>
      <input type="button" v-on:click="createAIML()" name="" value="create">
    </div>
    <div class="qiita_wrap">
      <!-- v-forで最新の5件を取得 -->
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
      this.url = `https://35peq81ai3.execute-api.ap-northeast-1.amazonaws.com/prod/nodeCGSsample`
      axios
        .post(`${this.url}`)
        .then(response => console.log(response))
    },
    createAIMLTEST () {
      axios({
        url: 'https://35peq81ai3.execute-api.ap-northeast-1.amazonaws.com/prod/nodeCGSsample',
        method: 'GET',
        responseType: 'blob'
      }).then((response) => {
        const url = URL.createObjectURL(new Blob([response.data]))
        const link = document.createElement('a')
        link.href = url
        link.setAttribute('download', 'file.txt')
        document.body.appendChild(link)
        link.click()
        link.revokeObjectURL()
      })
    },
    createAIML () {
      let method = document.getElementById('method_data').value
      let url = document.getElementById('url_data').value
      let header = document.getElementById('header_data').value
      let redive1 = '{\n' + '"method": ' + method + ',\n'
      let redive2 = '"url": ' + url + ',\n'
      let redive3 = '"header": ' + header + ',\n' + '}'
      const result = redive1 + redive2 + redive3
      const downloadFileName = 'downloadtest.xml'
      const blob = new Blob([result], { 'type': 'text/xml' })
      if (window.navigator.msSaveBlob) {
        // IE, Edge
        window.navigator.msSaveOrOpenBlob(blob, downloadFileName)
      } else {
        // Chrome, FireFox
        const link = document.createElement('a')
        link.setAttribute('download', downloadFileName)
        link.href = URL.createObjectURL(blob)
        const evt = document.createEvent('MouseEvents')
        evt.initEvent('click', false, true)
        link.dispatchEvent(evt)
      }
    }
  },
  // ページが読み込まれた際に実行したい{処理}
  created () {
    this.getApi()
  }
}
</script>
