<template>
    <ul class='stack-container'>
      <li v-for="(item, index) in pages"
      :style="[styleIndex(index),transform(index)]"
      @touchmove="touchmove"
      @touchstart="touchstart"
      @touchend="touchend"
      @mousedown="touchstart"
      @mouseup="touchend"
      @mousemove="touchmove"
      @webkit-transition-end="onTransitionEnd(index)"
      @transitionend="onTransitionEnd(index)">
        <div v-html="item.html"></div>
      </li>
    </ul>
</template>
<script>
import detectPrefixes from '../utils/detect-prefixes.js'
export default {
  props: ['stackinit', 'pages'],
  data () {
    return {
      basicdata: {
        start: {},
        end: {}
      },
      temporaryData: {
        prefixes: detectPrefixes(),
        poswidth: '',
        posheight: '',
        lastPosWidth: '',
        lastPosHeight: '',
        visible: this.stackinit.visible || 3,
        tracking: false,
        animation: false,
        currentPage: this.stackinit.currentPage || 0,
        currentSwipe: false,
        opacity: 1,
        lastOpacity: 0,
        swipe: false,
        zIndex: 10
      }
    }
  },
  computed: {
  },
  mounted () {
  },
  methods: {
    touchstart (e) {
      if (this.temporaryData.tracking) {
        return
      }
      if (e.type === 'touchstart') {
        if (e.touches.length > 1) {
          this.temporaryData.tracking = false
          return
        } else {
          /* Hack - would normally use e.timeStamp but it's whack in Fx/Android */
          this.basicdata.start.t = new Date().getTime()
          this.basicdata.start.x = e.targetTouches[0].clientX
          this.basicdata.start.y = e.targetTouches[0].clientY
          this.basicdata.end.x = e.targetTouches[0].clientX
          this.basicdata.end.y = e.targetTouches[0].clientY
        }
      } else {
        /* Hack - would normally use e.timeStamp but it's whack in Fx/Android */
        this.basicdata.start.t = new Date().getTime()
        this.basicdata.start.x = e.clientX
        this.basicdata.start.y = e.clientY
        this.basicdata.end.x = e.clientX
        this.basicdata.end.y = e.clientY
      }
      this.temporaryData.tracking = true
      this.temporaryData.animation = false
    },
    touchmove (e) {
      if (this.temporaryData.tracking && !this.temporaryData.animation) {
        if (e.type === 'touchmove') {
          e.preventDefault()
          this.basicdata.end.x = e.targetTouches[0].clientX
          this.basicdata.end.y = e.targetTouches[0].clientY
        } else {
          e.preventDefault()
          this.basicdata.end.x = e.clientX
          this.basicdata.end.y = e.clientY
        }
        this.temporaryData.poswidth = this.basicdata.end.x - this.basicdata.start.x
        this.temporaryData.posheight = this.basicdata.end.y - this.basicdata.start.y
      }
    },
    touchend (e) {
      this.temporaryData.tracking = false
      this.temporaryData.animation = true
      if (Math.abs(this.temporaryData.poswidth) >= 200) {
        this.temporaryData.poswidth = this.temporaryData.poswidth >= 0 ? this.temporaryData.poswidth + 200 : this.temporaryData.poswidth - 200
        this.temporaryData.lastPosWidth = this.temporaryData.poswidth
        this.temporaryData.lastPosHeight = this.temporaryData.posheight
        this.temporaryData.opacity = 0
        this.temporaryData.swipe = true
        this.temporaryData.currentPage = this.temporaryData.currentPage < this.pages.length - 1 ? this.temporaryData.currentPage + 1 : 0
        this.$nextTick(() => {
          this.temporaryData.poswidth = 0
          this.temporaryData.posheight = 0
          this.temporaryData.opacity = 1
        })
      } else {
        this.temporaryData.poswidth = 0
        this.temporaryData.posheight = 0
        this.temporaryData.swipe = false
      }
    },
    onTransitionEnd (index) {
      if (this.temporaryData.swipe && index === this.temporaryData.currentPage - 1) {
        this.temporaryData.animation = true
        this.temporaryData.lastPosWidth = 0
        this.temporaryData.lastPosHeight = 0
        this.temporaryData.lastOpacity = 0
        this.temporaryData.swipe = false
      }
    },
    transform (index) {
      if (index > this.temporaryData.currentPage) {
        let style = {}
        let visible = this.temporaryData.visible
        let perIndex = index - this.temporaryData.currentPage
        if (index <= this.temporaryData.currentPage + visible - 1) {
          style['opacity'] = '1'
          style['transform'] = 'translate3D(0,0,' + -1 * perIndex * 60 + 'px' + ')'
          style['zIndex'] = visible - index + this.temporaryData.currentPage
          style[this.temporaryData.prefixes.transition + 'TimingFunction'] = 'ease'
          style[this.temporaryData.prefixes.transition + 'Duration'] = 300 + 'ms'
        } else {
          style['zIndex'] = '-1'
          style['transform'] = 'translate3D(0,0,' + -1 * visible * 60 + 'px' + ')'
        }
        return style
      } else if (index === this.temporaryData.currentPage - 1) {
        let style = {}
        style['transform'] = 'translate3D(' + this.temporaryData.lastPosWidth + 'px' + ',' + this.temporaryData.lastPosHeight + 'px' + ',0px)'
        style['opacity'] = this.temporaryData.lastOpacity
        style['zIndex'] = -1
        style[this.temporaryData.prefixes.transition + 'TimingFunction'] = 'ease'
        style[this.temporaryData.prefixes.transition + 'Duration'] = 300 + 'ms'
        return style
      }
    },
    styleIndex (index) {
      if (index === this.temporaryData.currentPage) {
        let style = {}
        style['transform'] = 'translate3D(' + this.temporaryData.poswidth + 'px' + ',' + this.temporaryData.posheight + 'px' + ',0px)'
        style['opacity'] = this.temporaryData.opacity
        style['zIndex'] = 10
        if (this.temporaryData.animation) {
          style[this.temporaryData.prefixes.transition + 'TimingFunction'] = 'ease'
          style[this.temporaryData.prefixes.transition + 'Duration'] = (this.temporaryData.animation ? 300 : 0) + 'ms'
        }
        return style
      }
    },
    isQueue (index) {
      // let currentPage = this.temporaryData.currentPage
      // let visible = this.temporaryData.visible
      // let pageLength = this.pages.length
    }
  }
}
</script>
<style>
  .stack-container{
    position: relative;
    margin: 40px auto;
    padding: 0;
    width: 340px;
    height: 380px;
    list-style: none;
    -webkit-perspective: 1000px;
    -webkit-perspective-origin: 50% 150%;
    perspective: 1000px;
    perspective-origin: 50% 150%;
  }
  .stack-container li{
    position: absolute;
    z-index: 1;
    width: 340px;
    height: 380px;
    border-width: 60px 20px;
    border-style: solid;
    border-color: #fff;
    box-shadow: 0 10px 7px -7px rgba(0,0,0,0.12), 0 0 4px rgba(0,0,0,0.1);
    opacity: 0;
    cursor: pointer;
/*    -webkit-transform: translate3d(0,0,-180px);
    transform: translate3d(0,0,-180px);
    -webkit-transform-style: preserve-3d;
    transform-style: preserve-3d;*/
  }
  .stack-container li img {
    display: block;
  }
  .stack-container li h5 {
    margin: 0 5px;
    color: #143f51;
    height: 60px;
    text-align: right;
    font-size: 1.4em;
    font-family: "Sacramento", cursive;
    line-height: 60px;
  }
  .stack-container li.animate {
    -webkit-transition: all 0.3s ease-out;
    transition: all 0.3s ease-out;
  }
  .stack-container li.move-back {
    /* http://matthewlein.com/ceaser/ */
    -webkit-transition-timing-function: cubic-bezier(0.175, 0.885, 0.470, 1); /* older webkit */
    -webkit-transition-timing-function: cubic-bezier(0.175, 0.885, 0.470, 1.515);
    transition-timing-function: cubic-bezier(0.175, 0.885, 0.470, 1.515);
  }
</style>