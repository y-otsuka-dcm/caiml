<template>
    <div class="rss" id="ras">
    <h1 class="tit">Create AIML Tools</h1>
    <p class="txt">GET/POST確認用</p>
    <div class="getpost_test">
      <b-button input type="button" size="lg" variant="outline-secondary" v-on:click="postUrl()" name="">PostAPI</b-button>
      <b-button input type="button" size="lg" variant="outline-secondary" v-on:click="createAIMLTEST()" name="">DLTest</b-button>
    </div>
    <div id="app">
  <form action="" @submit.prevent="validate">
    <div class="form-group">
      <label for="">method</label>
      <input name="method" type="text" id="method_data" class="form-control" v-validate="'required'" />
      <span class="text-danger">{{ errors.first('method') }}</span>
    </div>
    <div class="form-group">
      <label for="">url</label>
      <input name="url" type="text" id="url_data" class="form-control" v-validate="'required'" />
      <span class="text-danger">{{ errors.first('url') }}</span>
    </div>
    <div class="form-group">
      <label for="">header</label>
      <input name="header" type="text" id="header_data" class="form-control" v-validate="'required'" />
      <span class="text-danger">{{ errors.first('header') }}</span>
    </div>
    <button type="submit" class="btn btn-primary" v-on:click="Validate()" name="">Create</button>
  </form>
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
    Validate () {
      this.$validator.validateAll()
        .then((result) => {
          if (result) {
            this.createAIML()
          }
        })
    },
    createAIML () {
      const method = document.getElementById('method_data').value
      const url = document.getElementById('url_data').value
      const header = document.getElementById('header_data').value
      const redive = '        {\n' + '          "method": "' + method + '",\n' + '          "url": "' + url + '",\n' + '          "header: {' + header + '}\n' + '        }'
      const tag1 = '<category>\n' + '  <pattern>' + '汎用CGS' + '</pattern>\n' + '  <template>\n' + '    <!--' + ' 汎用CGS「multicgs」を実行します。 ' + '-->\n' + '    <ext name="multicgs">\n' + '      <!-- リクエストパラメータを設定します。 -->\n' + '      <arg name="request">\n'
      const tag2 = '\n      </arg>\n' + '    </ext>\n' + '    <!-- 実行結果を取得します。 -->\n' + '    <think><predstore>location.setJson(<get name="_ext_multicgs_body"/>)</predstore></think>\n' + '    <predstore>location.stateName</predstore>\n' + '  </template>\n' + '</category>'
      const sum = tag1 + redive + tag2
      const downloadFileName = 'aimldata.xml'
      const blob = new Blob([sum], { 'type': 'text/xml' })
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
    this.postUrl()
  }
}
</script>
