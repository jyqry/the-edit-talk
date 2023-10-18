<template>
  <div id="chat-setting">
    <div class="chat-preview">
      <draggable v-model="getCurrentPage.chats">
        <div class="line" v-for="(chat, index) in getCurrentPage.chats">
          <div class="name">
            #{{chat.profile}} {{ getProfiles[chat.profile].name }}
            <button class="delete" @click="deleteChatLine(index)">
              삭제
            </button>
          </div>
          <div class="text">
            <textarea :value="chat.text" @change="updateChatLine(index, $event)"></textarea>
          </div>
        </div>
      </draggable>
    </div>
    <div class="input-form">
      <div class="profile-selector">
        <select @change="profileChanged">
          <option v-for="(profile, index) in getProfiles" :value="index">#{{index}} {{ profile.name }}</option>
        </select>
        <div class="text">
          <textarea v-model="currentChat" ref="chatTextarea" @keypress.shift.enter="insertChatLineByShortCut"></textarea>
        </div>
        <button class="submit" @click="insertChatLine">
          입력 (shift+enter)
        </button>
      </div>
    </div>
  </div>
</template>

<script>
import draggable from 'vuedraggable'

export default {
  name: 'chat-setting',
  components: {
    draggable
  },
  data () {
    return {
      profileIndex: 0,
      currentChat: ''
    }
  },
  methods: {
    updatePageTitle: function (e) {
      this.$store.dispatch('updatePageTitle', e.target.value)
    },
    pageThemeChange: function (data) {
      this.$store.dispatch('pageThemeChange', data)
    },
    profileChanged: function (e) {
      this.profileIndex = e.target.value
    },
    insertChatLine: function () {
      var data = {
        profile: this.profileIndex,
        text: this.currentChat.trim()
      }
      if (this.currentChat !== '') {
        this.$store.dispatch('insertChatLine', data)
      }
      this.currentChat = ''
      console.log(this.getCurrentPage.chats.length)
      if (this.getCurrentPage.chats.length > 19) {
        console.log('?')
        this.newPage()
        alert('채팅이 20줄을 넘었습니다. 새 페이지를 생성합니다.')
      }
    },
    newPage: function () {
      this.$store.dispatch('newPage')
      this.pageChange(this.getPages.length - 1)
    },
    pageChange: function (index) {
      this.$store.dispatch('pageChange', index)
    },
    updateChatLine: function (index, e) {
      var data = {
        index: index,
        text: e.target.value
      }
      this.$store.dispatch('updateChatLine', data)
    },
    insertChatLineByShortCut: function () {
      this.insertChatLine()
      this.currentChat = ''
      this.$refs.chatTextarea.value = null
    },
    deleteChatLine: function (index) {
      this.$store.dispatch('deleteChatLine', index)
    },
    currentChatUpdate: function (e) {
      this.currentChat = e.target.value
      console.log(e.target.value)
    }
  },
  computed: {
    getProfiles: function () {
      return this.$store.getters.profiles
    },
    getCurrentPage: function () {
      return this.$store.getters.currentPage
    },
    getPages: function () {
      return this.$store.getters.pages
    },
    chats: {
      get () {
        return this.$store.getters.currentPage.chats
      },
      set (value) {
        this.$store.commit('SET_CURRENT_CHAT', value)
      }
    }
  }
}
</script>

<style scoped lang="sass">
#chat-setting
  width: 100%
  height: 100%
  box-sizing: border-box
  padding-bottom: 110px
  .chat-preview
    width: 100%
    height: 100%
    box-sizing: border-box
    overflow: auto
    .line
      padding: 5px
      border-bottom: 1px solid #e6e6e6
      font-size: 14px
      .name
        font-weight: bold
      .text
        textarea
          width: 100%
          height: 40px
          box-sizing: border-box
      .delete
        color: #ff0000

  .input-form
    position: absolute
    left: 0
    bottom: 0
    width: 100%
    height: 110px
    background: #ffffff
    box-sizing: border-box
    border-top: 1px solid #e6e6e6
    select
      width: 100%
      height: 25px
    .text
      height: 45px
      textarea
        width: 100%
        height: 100%
        border: 0
        box-sizing: border-box
    .submit
      width: 100%
      height: 40px
      line-height: 40px
      border: 0
      border-top: 1px solid #e6e6e6
      text-align: center
      box-sizing: border-box
      color: #009EE7
      cursor: pointer
      outline: none
</style>
