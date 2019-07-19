<template>
    <div class="rss">
    <h1 class="tit">Create AIML くん</h1>
    <p class="txt_gp">GET/POST確認用ボタン</p>
    <div class="getpost_test">
      <button type="submit" class="btn btn-primary" v-on:click="postUrl()" name="">PostAPI</button>
      <button type="submit" class="btn btn-primary" v-on:click="dlTEST()" name="">DLTest</button>
    </div>
    <div class="wrapper">
    <div class="demo">
    <span>AIML DATA DEMO</span>
    <p style="white-space: pre-wrap;">{{aiml_data}}</p>
    </div>
  <form action="" @submit.prevent="validate">
    <span>FORM</span>
    <div class="form-group">
    <li>method</li>
    <select v-model="selected" id="select_method_data">
      <option name="method" type="text" id="method_data" class="form-controll" v-validate="'required'">GET</option>
      <option name="method" type="text" id="method_data" class="form-controll" v-validate="'required'">POST</option>
      <option name="method" type="text" id="method_data" class="form-controll" v-validate="'required'">DELETE</option>
      <option name="method" type="text" id="method_data" class="form-controll" v-validate="'required'">PATCH</option>
      <span class="text-danger">{{ errors.first('method') }}</span>
    </select>
    </div>
    <div class="form-group">
      <li>url</li>
      <input name="url" type="text" id="url_data" class="form-controll" v-validate="'required'" />
      <span class="text-danger">{{ errors.first('url') }}</span>
    </div>
    <div class="form-group">
      <li>header</li>
      <input name="header" type="text" id="header_data" class="form-controll" v-validate="'required'" />
      <span class="text-danger">{{ errors.first('header') }}</span>
    </div>
    <button type="submit" class="btn btn-primary" v-on:click="Validate()" name="">Create</button>
    <button type="submit" class="btn btn-primary" v-on:click="createAIMLTEST()" name="">CreateTest</button>
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
      tag: '',
      aiml_data: ''
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
    dlTEST () {
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
    createAIMLTEST () {
      this.url = `https://35peq81ai3.execute-api.ap-northeast-1.amazonaws.com/prod/nodeCGSsample`
      const message = 'ここにAPIで取得したmessage候補が入る(これをユーザーが選べるようにしたかった・・・)'
      const method = document.getElementById('select_method_data').value
      const url = document.getElementById('url_data').value
      const header = document.getElementById('header_data').value
      const redive = '        {\n' + '          "method": "' + method + '",\n' + '          "url": "' + url + '",\n' + '          "header: {' + header + '}\n' + '        }'
      const tag1 = '<category>\n' + '  <pattern>' + '汎用CGS' + '</pattern>\n' + '  <template>\n' + '    <!--' + ' 汎用CGS「multicgs」を実行します。 ' + '-->\n' + '    <ext name="multicgs">\n' + '      <!-- リクエストパラメータを設定します。 -->\n' + '      <arg name="request">\n'
      const tag2 = '\n      </arg>\n' + '    </ext>\n' + '    <!-- 実行結果を取得します。 -->\n' + '    <think><predstore>' + message + '.setJson(<get name="_ext_multicgs_body"/>)</predstore></think>\n' + '    <predstore>location.stateName</predstore>\n' + '  </template>\n' + '</category>'
      const sum = tag1 + redive + tag2
      this.aiml_data = sum
    },
    createAIML () {
      const method = document.getElementById('select_method_data').value
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

<style scoped>
div .rss{
  font-family: 'klee';
  width: auto;
  height: 804px;
}

.rss {
    font-family: 'klee';
    background:linear-gradient(to right, rgb(255, 153, 0) 0%, rgb(208, 255, 0) 100%);
    background-repeat: auto;
}

.tit {
  text-align: center;
}

.txt_gp {
  text-align: center;
}

.getpost_test {
  text-align: center;
}

.form_group {
  position: relative;
  top: 120px;
  left: 10px;
}

.demo {
  position: absolute;
  left: 726px;
}

.form_controll {
  width: 50%;
}
</style>
