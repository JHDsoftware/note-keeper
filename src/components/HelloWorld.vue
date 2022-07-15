<template>
  <v-container>
    <v-row>
      <v-col cols="12">

        <div  class="pa-4" style="min-height: calc(100vh - 172px)">
          <h2>新建笔记</h2>
          <v-card elevation="0" min-height="120px" ref="target" color="#f6f6f6" class="mt-4 pa-4">
            <v-img width="100%"
                   contain
                   v-if="uploadUrl" :src="uploadUrl"></v-img>
            <h1>{{title}}</h1>
            <div>{{content}}</div>
          </v-card>
          <v-file-input
              name="file"
              v-model="file"
              prepend-icon="mdi-image"
              accept="image/*"
              label="上传图片"
          />
          <v-text-field label="标题" v-model="title"></v-text-field>
          <v-textarea label="内容" name="body" v-model="content" counter="500"></v-textarea>
        </div>
        <v-btn download v-if="linkReady" ref="link" block color="primary" large elevation="0"
               :href="imgUrl"
               class="mt-4">下载图片
        </v-btn>
        <v-btn v-else @click="sendMail" block dark large elevation="0" class="mt-4">
          生成
          <v-icon right>mdi-send</v-icon>
        </v-btn>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>/**
 *
 * @param file
 * @returns {string}
 */

import html2canvas from 'html2canvas'

function uploadImg (file) {
  return URL.createObjectURL(file)
}



export default {
  data: () => ({
    file: null,
    title: '',
    content: '',
    imgUrl: '',
    linkReady: false
  }),
  methods: {
    async sendMail () {
      await this.refreshPicture()
      this.linkReady = true
    },
    async refreshPicture () {
      try {
        console.log(this.$refs.target)
        const canvas=await html2canvas(this.$refs.target.$el)
        console.log(this.content,'content')
        const newImg =await new Promise(resolve => canvas.toBlob(resolve))
        console.log(newImg)
        const currentUrl = (uploadImg(newImg))
        console.log(currentUrl)
        this.imgUrl = currentUrl
      }catch (e) {
        console.log(e,'error')
      }

    }
  },
  watch:{
    title(){
      this.linkReady=false
    },
    content(){
      this.linkReady=false
    },
    file(){
      this.linkReady=false
    }
  },
  computed: {
    uploadUrl: function () {
      console.log(this.file)
      return this.file ? URL.createObjectURL(this.file) : null
    }
  }

}
</script>
