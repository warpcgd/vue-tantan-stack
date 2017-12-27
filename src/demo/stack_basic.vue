<template>
  <stack :pages="someList" :stackinit="stackinit"></stack>
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
          html: '<img src="src/img/1.jpg" alt="01"><h5>Saudade</h5>'
        },
        {
          html: '<img src="src/img/2.jpg" alt="01"><h5>Saudade</h5>'
        },
        {
          html: '<img src="src/img/3.jpg" alt="03"><h5>Saudade</h5>'
        },
        {
          html: '<img src="src/img/4.jpg" alt="01"><h5>Saudade</h5>'
        },
        {
          html: '<img src="src/img/5.jpg" alt="01"><h5>Saudade</h5>'
        },
        {
          html: '<img src="src/img/6.jpg" alt="01"><h5>Saudade</h5>'
        }
      ]
    }, 2000)
  },
  components: {
    stack
  }
}
</script>
