<template>
  <transition name="slide">
    <div class="add-song" v-show="showFlag" @click.stop>
      <div class="header">
        <h1 class="title">添加歌曲到列表</h1>
        <div class="close" @click="hide">
          <i class="icon-close"></i>
        </div>
      </div>
      <div class="search-box-wrapper">
        <search-box ref="searchBox" @query="onQueryChange" placeholder="搜索歌曲"></search-box>
      </div>
      <div class="shortcut" v-show="!query">
        <switches :switches="switches" :currentIndex="currentIndex" @switch="switchItem"></switches>
        <div class="list-wrapper">
          <scroll ref="songList" v-if="currentIndex===0" class="list-scroll" :data="playHistory">
            <div class="list-inner">
              <song-list :songs="playHistory" @select="selectSong">
              </song-list>
            </div>
          </scroll>
          <scroll :refreshDelay="refreshDelay" ref="searchList" v-if="currentIndex===1" class="list-scroll"
                  :data="searchHistory">
            <div class="list-inner">
              <search-list @delete="deleteSearchHistory" @select="addQuery" :searches="searchHistory"></search-list>
            </div>
          </scroll>
        </div>
      </div>
      <div class="search-result" v-show="query">
        <suggest :query="query" :showSinger="showSinger" @select="selectSuggest" @listScroll="blurInput"></suggest>
      </div>
      <top-tip ref="topTip">
        <div class="tip-title">
          <i class="icon-ok"></i>
          <span class="text">1首歌曲已经添加到播放列表</span>
        </div>
      </top-tip>
    </div>
  </transition>
</template>

<script type="text/ecmascript-6">
  import SearchBox from 'base/search-box/search-box'
  import SongList from 'base/song-list/song-list'
  import SearchList from 'base/search-list/search-list'
  import Scroll from 'base/scroll/scroll'
  import Switches from 'base/switches/switches'
  import TopTip from 'base/top-tip/top-tip'
  import Suggest from 'components/suggest/suggest'
  import {searchMixin} from 'common/js/mixin'
  import {mapGetters, mapActions} from 'vuex'
  import Song from 'common/js/song'

  export default {
    mixins: [searchMixin],
    data() {
      return {
        showFlag: false,
        showSinger: false,
        currentIndex: 0,
        songs: [],
        switches: [
          {
            name: '最近播放'
          },
          {
            name: '搜索历史'
          }
        ]
      }
    },
    computed: {
      ...mapGetters([
        'playHistory'
      ])
    },
    created() {
      // let arr = [1, 2, 3, 4, 2, 3, Symbol(2)]
      // let arr1 = [{a: 1}, {c: 2}, {c: 2}, 4, 2, 3, Symbol(2)]
      // // console.log(Array.isArray(arr1))
      // let res = arr1.filter((n, i) => {
      //   // return arr.indexOf(n) === i
      //   return arr1.findIndex(item => item === n) === i
      // })
      // console.log()
      // console.log(res, 'res')

      // console.log([...new Set(arr1)])

      // let obj = {a: 5}
      // Object.freeze(obj, 'a', 15)
      // console.log(obj, 'obj')
      // var func = []
      // for (var i = 0; i < 10; i++) {
      //   func.push((function(n) {
      //     console.log(n)
      //   })(i))
      // }
      // console.log(window)
      // function* fibs() {
      //   let a = 0
      //   let b = 1
      //   while (true) {
      //     yield a;
      //     [a, b] = [b, a + b]
      //   }
      // }

      // let [first, second, third, fourth, fifth, sixth] = fibs()
      // console.log(first, second, third, fourth, fifth, sixth)

      // class RangeIterator {
      //   // 构造器
      //   constructor(start, stop) {
      //     this.value = start
      //     this.stop = stop
      //     // this.next = () => {
      //     //   var value = this.value
      //     //   if (value < this.stop) {
      //     //     this.value++
      //     //     return {done: false, value: value}
      //     //   }
      //     //   return {done: true, value: undefined}
      //     // }
      //   }

      //   [Symbol.iterator]() {
      //     let _this = this
      //     console.dir(this)
      //     return {
      //       next: function () {
      //         var value = _this.value
      //         if (value < _this.stop) {
      //           _this.value++
      //           return {done: false, value: value}
      //         }
      //         return {done: true, value: undefined}
      //       }
      //     }
      //   }

      // }

      // function range(start, stop) {
      //   return new RangeIterator(start, stop)
      // }
      // let a1 = range(0, 3)
      // console.log(a1[Symbol.iterator]().next())
      // console.log(a1[Symbol.iterator]().next())
      // console.log(a1[Symbol.iterator]().next())
      // console.log(a1[Symbol.iterator]().next())
      // console.log(a1.next)
      // for (var value of range(0, 3)) {
      //   console.log(value) // 0, 1, 2
      // }

      // function* fibs() {
      //   let a = 0
      //   let b = 1
      //   while (true) {
      //     [a, b] = [b, a + b]
      //     yield [a, b]
      //   }
      // }

      // let t1 = fibs()
      // console.log(t1[Symbol.iterator]().next())
      // console.log(t1[Symbol.iterator]().next())
      // console.log(t1[Symbol.iterator]().next())
      // console.log(t1[Symbol.iterator]().next())
      // console.log(t1[Symbol.iterator]().next())
      // console.log(t1[Symbol.iterator]().next())

      // // 0, 1
      // // 1, 1
      // // 1, 2
      // // 2, 3
      // // 3, 5
      // // 5, 8

      // let [first, second, third, fourth, fifth, sixth] = fibs()
      // console.log(first, second, third, fourth, fifth, sixth)
      // // sixth // 5

      function* f() {
        console.log('执行了！')
        console.log('提交不上去了吗')
      }

      var generator = f()
      console.log(generator)
      console.log(generator.next())

      // aaaa
      // setTimeout(function () {
      //   generator.next()
      // }, 2000)
    },
    methods: {
      show() {
        this.showFlag = true
        setTimeout(() => {
          if (this.currentIndex === 0) {
            this.$refs.songList.refresh()
          } else {
            this.$refs.searchList.refresh()
          }
        }, 20)
      },
      hide() {
        this.showFlag = false
      },
      selectSong(song, index) {
        if (index !== 0) {
          this.insertSong(new Song(song))
          this.$refs.topTip.show()
        }
      },
      selectSuggest() {
        this.$refs.topTip.show()
        this.saveSearch()
      },
      switchItem(index) {
        this.currentIndex = index
      },
      ...mapActions([
        'insertSong'
      ])
    },
    components: {
      SearchBox,
      SongList,
      SearchList,
      Scroll,
      Switches,
      TopTip,
      Suggest
    }
  }
</script>

<style scoped lang="stylus" rel="stylesheet/stylus">
  @import "~common/stylus/variable"
  @import "~common/stylus/mixin"

  .add-song
    position: fixed
    top: 0
    bottom: 0
    width: 100%
    z-index: 200
    background: $color-background
    &.slide-enter-active, &.slide-leave-active
      transition: all 0.3s
    &.slide-enter, &.slide-leave-to
      transform: translate3d(100%, 0, 0)
    .header
      position: relative
      height: 44px
      text-align: center
      .title
        line-height: 44px
        font-size: $font-size-large
        color: $color-text
      .close
        position: absolute
        top: 0
        right: 8px
        .icon-close
          display: block
          padding: 12px
          font-size: 20px
          color: $color-theme

    .search-box-wrapper
      margin: 20px
    .shortcut
      .list-wrapper
        position: absolute
        top: 165px
        bottom: 0
        width: 100%
        .list-scroll
          height: 100%
          overflow: hidden
          .list-inner
            padding: 20px 30px
    .search-result
      position: fixed
      top: 124px
      bottom: 0
      width: 100%
    .tip-title
      text-align: center
      padding: 18px 0
      font-size: 0
      .icon-ok
        font-size: $font-size-medium
        color: $color-theme
        margin-right: 4px
      .text
        font-size: $font-size-medium
        color: $color-text
</style>
