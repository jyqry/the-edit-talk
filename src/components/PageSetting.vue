<template>
  <div id="page-setting">
    <div class="settings">
      <h2>채팅방 제목</h2>
      <div class="title">
        <input type="text" class="cm-input" :value="getCurrentPage.title" @input="updatePageTitle">
      </div>
      <h2>채팅방 폰트</h2>
      <div class="fonts">
        <div>
          <select @change="updatePageFont">
            <option value='font-default'>노토산스</option>
            <option value='font-apple'>애플고딕</option>
            <option value='font-malgun'>맑은고딕</option>
            <option value='font-nanum'>나눔고딕</option>
            <option value='font-nanum-mj'>나눔명조</option>
          </select>
        </div>
        <div>
          <input type="checkbox" @change="updatePageFontWeight" id="font-weight-bold"><label for="font-weight-bold">굵은 텍스트</label>
        </div>
        <div>
          <label><input type="checkbox" v-model="nameShow" @change="updatePageNameShow">이름 항상 표시</label>
        </div>
      </div>
      <h2>인터페이스 표시</h2>
      <div class="interfaces">
        <div>
          <label><input type="checkbox" @change="toggleInterfaceTop">상단 인터페이스 표시</label>
        </div>
        <div>
          <label><input type="checkbox" @change="toggleInterfaceBottom">하단 인터페이스 표시</label>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'page-setting',
  data () {
    return {
      fontWeight: false,
      nameShow: true
    }
  },
  methods: {
    updatePageTitle: function (e) {
      this.$store.dispatch('updatePageTitle', e.target.value)
    },
    updatePageFont: function (e) {
      this.$store.dispatch('updatePageFont', e.target.value)
    },
    updatePageFontWeight: function () {
      this.fontWeight = !this.fontWeight
      this.$store.dispatch('updatePageFontWeight', this.fontWeight)
    },
    updatePageNameShow: function () {
      this.nameShow = !this.nameShow
      this.$store.dispatch('updatePageNameShow', this.nameShow)
    },
    pageThemeChange: function (data) {
      this.$store.dispatch('pageThemeChange', data)
    },
    toggleInterfaceTop: function () {
      this.$store.dispatch('toggleInterfaceTop')
    },
    toggleInterfaceBottom: function () {
      this.$store.dispatch('toggleInterfaceBottom')
    }
  },
  computed: {
    getChat: function () {
      return this.$store.getters.chat
    },
    getCurrentPage: function () {
      return this.$store.getters.currentPage
    }
  }
}
</script>

<style scoped lang="sass">
#page-setting
  width: 100%
  .themes
    .theme
      float: left
      width: 20px
      height: 20px
      border-radius: 20px
      margin: 1px 5px 5px 5px
      box-sizing: border-box
      &.active
        width: 22px
        height: 22px
        border-radius: 22px
        border: 2px solid #000000
        margin-top: 0
      &.default
        background: #73b785
      &.kakao
        background: #FFEC42
      &.imessage
        background: #3478f6


</style>
