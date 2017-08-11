<template>
  <transition name="slider">
    <music-list :rank="rank" :title="title" :bgImage="bgImage" :songs="songList"></music-list>
  </transition>
</template>
<script type="text/ecmascript-6">
  import MusicList from 'components/music-list/music-list'
  import {getMusicList} from 'api/rank'
  import {ERR_OK} from 'api/config'
  import {createSong} from 'common/js/song'
  import {mapGetters} from 'vuex'
  export default {
    components: {
      MusicList
    },
    data () {
      return {
        rank: true,
        songList: []
      }
    },
    computed: {
      title() {
        return this.toplist.topTitle
      },
      bgImage() {
        if(this.songList.length > 0) {
          return this.songList[0].image
        }
        return ''
      },
      ...mapGetters([
        'toplist'
      ])
    },
    created() {
      this._getMusicList()
    },
    methods: {
      _getMusicList() {
        if(!this.toplist.id) {
          this.$router.push('/rank')
          return
        }
        getMusicList(this.toplist.id).then((res) => {
          if(res.code === ERR_OK) {
            this.songList = this._normalizeSongs(res.songlist)
          }
        })
      },
      _normalizeSongs(list) {
        let ret = []
        list.forEach((item) => {
          const musicData = item.data
          if(musicData.songid && musicData.albumid) {
            ret.push(createSong(musicData))
          }
        })
        return ret
      }
    }

  }
</script>
<style scoped lang="stylus" rel="stylesheet/stylus">
  .slider-enter-active, .slider-leave-active
    transition: all 0.3s ease

  .slider-enter, .slider-leave-active-to
    transform: translate3d(100%, 0, 0)
</style>
