<template>
  <div class="wrapper" ref="wrapper">
    <div class="content">
      <slot></slot>
    </div>
  </div>
</template>

<script>
  import BScroll from 'better-scroll'
  export default {
    name: "Scroll",
    props:{
      probeType:{
        type:Number,
        default:0
      },
      pullUpLoad:{
        type:Boolean,
        default:false
      }
    },
    data() {
      return {
        scroll: null
      }
    },
    mounted() {
      this.scroll = new BScroll(this.$refs.wrapper, {
        //有了click，scroll里面的div点击才能生效
        click:true,
        //有了probeType,scroll才能监听鼠标到底滚动到了哪个坐标
        probeType: this.probeType,
        //有了pullUpLoad，才能监听是否拉倒底部
        pullUpLoad: this.pullUpLoad
      }),
      this.scroll.on('scroll',(position)=>{
        this.$emit('scroll',position)
      }),
      this.scroll.on('pullingUp',()=>{
        this.$emit('pullingUp')
      })
    },
    methods:{
      scrollTo(x,y,time=300){
        console.log("wocao")
        this.scroll.scrollTo(x,y,time)
      },
      finishiPullUp(){
        this.scroll.finishPullUp()
      }
    }
  }
</script>

<style scoped>

</style>
