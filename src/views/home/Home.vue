<template>
    <div id="home" class="wrapper">
          <nav-bar class="home-nav"><div slot="center">购物街</div></nav-bar>
          <scroll class="content"
                ref="scroll"
                :probe-type="3"
                @scroll="contentScroll"
                :pull-up-load="true"
                @pullingUp="loadMore">
             <home-swiper :banners="banners"></home-swiper>
            <recommend-view :recommends="recommends"/>
            <feature-view />
            <tab-control :titles="['流行','新款','精选']" @tabClick="tabClick" class="tab-control"></tab-control>
            <goods-list :goods="showGoods"></goods-list>
          </scroll>
          <back-top @click.native="backClick" v-show="isShowBackTop"/>
    </div>
</template>

<script>
    import NavBar from "../../components/common/navbar/NavBar";
    import HomeSwiper from "./childComps/HomeSwiper";
    import TabControl from "../../components/content/tabControl/TabControl";
    import GoodsList from"../../components/content/goods/GoodsList";
    import BackTop from "../../components/content/backTop/BackTop";

    import {getHomeMultidata,getHomeGoods} from "network/home";
    import Swiper from "../../components/common/swiper/Swiper";
    import SwiperItem from "../../components/common/swiper/SwiperItem";
    import RecommendView from "./childComps/RecommendView";
    import Scroll from '../../components/common/scroll/Scroll'
    import FeatureView from "./childComps/FeatureView";
  //  import {Swiper, SwiperItem} from "components/commom/swiper/Swiper";
    export default {
        name: "Home",
        components:{
            RecommendView,
            NavBar,
            Swiper,
            SwiperItem,
            HomeSwiper,
            FeatureView,
            TabControl,
            GoodsList,
            Scroll,
            BackTop
        },
        data(){
          return {
              banners:[],
              recommends:[],
              goods: {
                  'pop': {page: 0, list: []},
                  'new': {page: 0, list: []},
                  'sell': {page: 0, list: []},
              },
              currentType : 'pop',
              isShowBackTop:true
          }
        },
        computed: {
            showGoods(){
                return this.goods[this.currentType].list;
            }
        },
        created:function() {
            this.getHomeMultidata()
            this.getHomeGoods('pop')
            this.getHomeGoods('new')
            this.getHomeGoods('sell')
        },
        methods:{
            tabClick(index){
                switch (index) {
                    case 0: this.currentType = 'pop';
                        break;
                    case 1: this.currentType = 'new';
                        break;
                    case 2: this.currentType = 'sell';
                        break;
                }
            },
            backClick(){
              this.$refs.scroll.scrollTo(0,0);
            },
            contentScroll(position){
                this.isShowBackTop = (-position.y) > 1000
            },
            loadMore() {
                this.getHomeGoods(this.currentType)

            },
            getHomeMultidata(){
                getHomeMultidata().then(res => {
                    // this.result = res;
                    this.banners = res.data.banner.list;
                    this.recommends = res.data.recommend.list;
                    console.log(this.banners);
                })
            },
            getHomeGoods(type){
                const page = this.goods[type].page+1
                getHomeGoods(type,page).then(res => {
                    this.goods[type].list.push(...res.data.list);
                    this.goods[type].page += 1;
                })
                this.$refs.scroll.finishiPullUp()
            }
        }
    }
</script>

<style scoped>
    #home {
        /*padding-top: 44px;*/
        height: 100vh;
        position: relative;
    }

    .home-nav {
        background-color: var(--color-tint);
        color: #fff;

        position: fixed;
        left: 0;
        right: 0;
        top: 0;
        z-index: 9;
    }

    .tab-control {
        position: sticky;
        top: 44px;
        z-index: 9;
    }

    .content {
        overflow: hidden;

        position: absolute;
        top: 44px;
        bottom: 49px;
        left: 0;
        right: 0;
    }


</style>
