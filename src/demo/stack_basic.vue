<template>
  <div class="mid-center">
    <div class="stack-wrapper">
      <stack :pages="someList" :stackinit="stackinit"></stack>
    </div>
    <div class="controls">
    </div>
  </div>
</template>
<script>
import stack from '../components/stack'
export default {
  el: '#stack',
  data () {
    return {
      someList: [],
      stackinit: {
        visible: 3
      }
    }
  },
  mounted () {
    let that = this
    setTimeout(function () {
      that.someList = [
        {
          html: '<img src="src/img/1.png" alt="01">'
        },
        {
          html: '<img src="src/img/2.png" alt="02">'
        },
        {
          html: '<img src="src/img/3.png" alt="03">'
        },
        {
          html: '<img src="src/img/4.png" alt="04">'
        },
        {
          html: '<img src="src/img/5.png" alt="05">'
        },
        {
          html: '<img src="src/img/6.png" alt="06">'
        },
        {
          html: '<img src="src/img/7.png" alt="07">'
        }
      ]
    }, 2000)
  },
  components: {
    stack
  }
}
</script>
<style>
  .stack-wrapper{
    margin: 0 auto;
    position: relative;
    z-index: 1000;
    width: 320px;
    height: 320px;
    padding: 0;
    list-style: none;
    pointer-events: none;
  }
  .mid-center{
  }
</style>
