<template>
  <div class="hello"  @dragover.prevent @drop="onDrop">
    <toolbar></toolbar>
    <div class="profile-wrap">
      <page-setting></page-setting>
      <profile></profile>
    </div>
    <div class="chat-setting-wrap">
      <chat-setting></chat-setting>
    </div>
    <div class="albums">
      <div class="how-to-use-album">
        <span class="code">img:숫자</span>를 쓰면 채팅창에 사진이 들어갑니다.
      </div>
      <div class="drop-here" v-if="album.length === 0">
        이미지 파일을 이곳에<br>
        드래그 앤 드롭 하세요.
      </div>
      <div class="pic" v-for="(pic, index) in album">
        <div>
          <img :src="pic">
        </div>
        <span>{{ index }}</span>
      </div>
    </div>
    <div class="chats">
      <chat></chat>
    </div>
    <div id="chat-render"></div>
  </div>
</template>

<script>
import Toolbar from '@/components/Toolbar'
import Profile from '@/components/Profile'
import PageSetting from '@/components/PageSetting'
import Chat from '@/components/Chat'
import ChatSetting from '@/components/ChatSetting'

export default {
  name: 'hello',
  components: {
    Toolbar,
    Profile,
    PageSetting,
    Chat,
    ChatSetting
  },
  data () {
    return {
    }
  },
  methods: {
    onDrop: function (e) {
      console.log(e)
      e.stopPropagation()
      e.preventDefault()
      var files = e.target.files || e.dataTransfer.files
      if (files) {
        for (let i = 0; i < files.length; i++) {
          var reader = new FileReader()
          reader.onloadend = (e) => {
            this.$store.dispatch('addAlbum', this.dataURItoBlob(e.target.result))
          }
          reader.readAsDataURL(files[i])
        }
      }
    },
    dataURItoBlob: function (dataURI) {
      var byteString = atob(dataURI.split(',')[1])
      // var mimeString = dataURI.split(',')[0].split(':')[1].split(';')[0]
      var ab = new ArrayBuffer(byteString.length)
      var ia = new Uint8Array(ab)
      for (var i = 0; i < byteString.length; i++) {
        ia[i] = byteString.charCodeAt(i)
      }

      var bb = new Blob([ab])
      return window.URL.createObjectURL(bb)
    }
  },
  computed: {
    getChat: function () {
      return this.$store.getters.chat
    },
    currentPage: function () {
      return this.$store.getters.currentPage
    },
    album: function () {
      return this.$store.getters.album
    }
  }
}
</script>

<style lang="sass">
.profile-wrap
  position: fixed
  top: 0
  left: 0
  width: 200px
  height: 100%
  box-sizing: border-box
  padding: 40px 10px 10px 10px
  background: #ffffff
  border-right: 1px solid #E6E6E6
  word-break: break-all
  overflow: auto
  z-index: 10
.chat-setting-wrap
  position: fixed
  top: 0
  left: 200px
  width: 200px
  height: 100%
  box-sizing: border-box
  padding: 40px 0 0 0
  background: #ffffff
  border-right: 1px solid #E6E6E6
  word-break: break-all
  z-index: 10
.albums
  position: fixed
  top: 0
  left: 400px
  width: 200px
  height: 100%
  box-sizing: border-box
  padding: 40px 0 0 0
  background-color: #ffffff
  border-right: 1px solid #e6e6e6
  z-index: 10
  overflow: auto
  .how-to-use-album
    width: 100%
    padding: 10px
    background-color: #009EE7
    color: #ffffff
    box-sizing: border-box
    font-size: 12px
    word-break: keep-all
    text-align: center
    line-height: 1.6
    .code
      background-color: #ffffff
      color: #000000
      border-radius: 99px
      padding: 0 5px
  .drop-here
    padding: 100px 10px
    box-sizing: border-box
    text-align: center
  .pic
    padding: 10px
    text-align: center
    img
      margin-bottom: 5px
      max-width: 180px
.chats
  margin-top: 40px
  margin-left: 600px
.dev
  div
    margin: 20px 0

h2
  font-weight: bold
  font-size: 16px
  margin: 20px 0

#chat-render
  position: absolute
  left: 200%
  top: 0
  .chat-preview
    zoom: 1 !important
</style>
