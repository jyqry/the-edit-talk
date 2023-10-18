<template>
  <div id="chat">
    <div class="save-button">
      <button @click="saveToPng">이미지로 저장</button>
    </div>
    <div id="chat-preview" class="chat-preview" :class="[getCurrentPage.theme, getCurrentPage.font]">
      <div v-if="getCurrentPage.title" class="page-title">
        {{ getCurrentPage.title }}
      </div>
      <div v-show="getCurrentPage.showInterfaceTop" class="imessage-header">
        <div class="top-right"></div>
        <div class="profile">
          <div class="thumb" v-bind:style="{ backgroundImage: `url('${getProfiles[0].pic}')`}"></div>
          <div class="name" v-text="getProfiles[0].name"></div>
        </div>
      </div>
      <div class="chat-list">
        <div class="line"
          v-for="(chat, index) in getCurrentPage.chats"
          v-bind:class="getProfiles[chat.profile].direction">

          <div class="pic"
            v-if="isLastProfile(index, chat.profile)"
            v-bind:style="{ 'background-image': 'url(' + getProfiles[chat.profile].pic + ')' }">
          </div>

          <div v-bind:class="{'text-wrap': Array.from(chat.text).length !== 1 || !hasEmoji(chat.text)}">
            <div class="name" v-if="isDuplicateProfile(index, chat.profile) && getCurrentPage.nameShow">
              {{ getProfiles[chat.profile].name }}
            </div>
            <div v-if="chat.text.startsWith('img:')" class="img">
              <img :src="getPhoto(chat.text.replace('img:', ''))" crossorigin="Anonymous">
            </div>
            <div v-else-if="!(Array.from(chat.text).length !== 1 || !hasEmoji(chat.text))" class="emoji" v-html="chat.text.replace(/\n/g, '<br>')"></div>
            <div v-else class="text" v-bind:class="{ bold : getCurrentPage.fontWeight, first: isDuplicateProfile(index, chat.profile), last: isLastProfile(index) }" v-html="chat.text.replace(/\n/g, '<br>')"></div>
          </div>

        </div>
      </div>
      <div class="bottom-bar" v-show="getCurrentPage.showInterfaceBottom"></div>
    </div>
  </div>
</template>

<script>
import html2canvas from 'html2canvas'

export default {
  name: 'chat',
  data () {
    return {
    }
  },
  methods: {
    updatePageTitle: function (e) {
      this.$store.dispatch('updatePageTitle', e.target.value)
    },
    pageThemeChange: function (data) {
      this.$store.dispatch('pageThemeChange', data)
    },
    saveToPng: function () {
      var image = new Image()
      image.crossOrigin = 'Anonymous'
      var origin = document.getElementById('chat-preview')
      var temp = origin.cloneNode(true)
      var filename = 'chat-' + this.getChat.currentPage + '-' + Date.now() + '.jpg'
      document.getElementById('chat-render').innerHTML = ''
      document.getElementById('chat-render').appendChild(temp)
      html2canvas(document.getElementById('chat-render').lastChild, { allowTaint: true }).then(function (canvas) {
        document.getElementById('chat-render').innerHTML = ''
        var data = canvas.toDataURL('image/jpeg', 0.9)
        window.download(data, filename, 'image/jpeg')
      })
    },
    isDuplicateProfile: function (before, after) {
      if (before === 0) {
        return true
      } else {
        return !(this.getCurrentPage.chats[before - 1].profile === after)
      }
    },
    isLastProfile: function (index) {
      // return false
      if (this.getCurrentPage.chats.length - 1 === index) {
        return true
      } else if (this.getCurrentPage.chats[index].profile !== this.getCurrentPage.chats[index + 1].profile) {
        return true
      }
      return false
    },
    getPhoto: function (index) {
      return this.album[index]
    },
    hasEmoji: function (str) {
      // const unifiedEmojiRanges = ['\ud83c[\udf00-\udfff]', '\ud83d[\udc00-\ude4f]', '\ud83d[\ude80-\udeff]']
      const reg = /([\uE000-\uF8FF]|\uD83C[\uDC00-\uDFFF]|\uD83D[\uDC00-\uDFFF]|[\u2694-\u2697]|\uD83E[\uDD10-\uDD5D])/g
      return reg.test(str)
    }
  },
  computed: {
    getProfiles: function () {
      return this.$store.getters.profiles
    },
    getChat: function () {
      return this.$store.getters.chat
    },
    getCurrentPage: function () {
      return this.$store.getters.currentPage
    },
    album: function () {
      return this.$store.getters.album
    }
  }
}
</script>

<style lang="sass" scoped>
.font-default
  font-family: 'Noto Sans KR'
.font-nanum
  font-family: 'Nanum Gothic'
.font-apple
  font-family: 'AppleSDGothicNeo-Regular'
.font-malgun
  font-family: 'Malgun Gothic'
.font-nanum-mj
  font-family: 'Nanum Myeongjo'

#chat
  background-color: #eee
  .save-button
    text-align: center
    padding: 20px
    button
      border: 0
      border-radius: 99px
      padding: 10px 20px
      margin: 0 auto
      background: #009EE7
      color: #ffffff
      cursor: pointer

#chat-preview.imessage
  zoom: 0.5
  width: 1125px
  min-height: 1800px
  height: auto
  margin: 0 auto 30px auto
  font-size: 38px
  box-sizing: border-box
  background: #ffffff
  &:after
    content: ''
    display: block
    clear: both
  .imessage-header
    position: relative
    background-color: #f9f9f9
    position: relative
    height: 330px
    border-bottom: 1px solid #b3b3b3
    .top-right
      position: absolute
      top: 0
      right: 0
      width: 1125px
      height: 300px
      background-image: url('/the-edit-talk/static/png/imessage-header-top-right.png')
      background-repeat: no-repeat
      background-size: 1125px 300px
      background-position: center
    .profile
      position: absolute
      top: 0
      left: 0
      z-index: 2
      width: 100%
      padding-top: 132px
      .thumb
        width: 120px
        height: 120px
        border-radius: 60px
        background-size: cover
        background-repeat: no-repeat
        background-position: center
        margin: 0 auto 12px auto
      .name
        text-align: center
        font-size: 35px
        color: #000000
  .page-title
    padding: 30px
    font-weight: bold
  .chat-list
    padding: 20px 0
    &:after
      content: ''
      display: block
      clear: both
    .line
      max-width: 80%
      position: relative
      clear: both
      margin-top: 3px
      margin-bottom: 3px
      box-sizing: border-box
      padding: 0 30px
      line-height: 1.5em
      &.left
        float: left
        .pic
          left: 35px
          bottom: -3px
        .emoji
          margin-left: 96px
          font-size: 100px
          height: 120px
          line-height: 1em
        .text-wrap
          margin-left: 109px
          .name
            text-align: left
          .text
            background: #e5e5ea
            &.last:before
              position: absolute
              left: -17px
              bottom: 0
              display: block
              content: ''
              width: 40px
              height: 36px
              background-image: url('/the-edit-talk/static/png/imessage-arrow-left.png')
              background-repeat: no-repeat
              background-size: cover
              background-position: center
      &.right
        float: right
        .pic
          display: none
        .emoji
          font-size: 100px
          height: 120px
          line-height: 1em
        .text-wrap
          margin-right: 20px
          .name
            text-align: right
            display: none
          .text
            background: #2394fe
            color: #ffffff
            &.last:before
              position: absolute
              right: -18px
              bottom: 0
              display: block
              content: ''
              width: 40px
              height: 40px
              background-image: url('/the-edit-talk/static/png/imessage-arrow-right.png')
              background-repeat: no-repeat
              background-size: cover
              background-position: center
      &:after
        content: ''
        display: block
        clear: both
      .pic
        position: absolute
        width: 80px
        height: 80px
        border-radius: 40px
        overflow: hidden
        background-size: cover
        background-repeat: no-repeat
        background-position: center
      .text-wrap
        margin-top: 0px
        .name
          margin-top: 20px
          margin-bottom: 13px
          line-height: 1em
          font-size: 33px
          color: #8f8e94
        .text
          max-width: 700px
          position: relative
          padding: 20px 38px
          border-radius: 48px
          box-shadow: none
          &.bold
            font-weight: bold
        .img
          max-width: 700px
          position: relative
          padding: 0
          box-shadow: none
          overflow: hidden
          font-size: 0
          img
            max-width: 100%
            border-radius: 50px
            vertical-align: top
        
  .bottom-bar
    width: 100%
    height: 127px
    background-image: url('/the-edit-talk/static/png/bottom-bar.png')
    background-repeat: no-repeat
    background-size: 100%
    background-position: center

</style>
