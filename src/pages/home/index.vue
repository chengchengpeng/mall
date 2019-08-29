<template>
  <div class="home">
    <header class="g-header-container">
    	<home-header :class="{'header-transition': isHeaderTransition}" ref="header"/>
    </header>
    <me-scroll 
    :data="recommends" 
    pullDown
    pullUp
    @pull-down="pullToRefresh"
    @pull-up="pullToLoadMore"
    @scroll-end="scrollEnd"
    @scroll="scroll"
    @pull-down-transition-end="pullDownTransitionEnd"
    ref="scroll">      
    <!-- <div> -->
      <home-slider ref="slider"/>
      <home-nav/>
      <home-recommend @loaded="getRecommends" ref= "recommend"/>
    </me-scroll>
    <!-- </div> -->
    <div class="g-backtop-container">
      <me-backtop :visible="isBacktopVisble" @backtop="backToTop"/>
    </div>
    <router-view>
    </router-view>
  </div>
</template>

<script>
  import HomeSlider from './slider';
  import MeBacktop from 'base/backtop';
  import HomeHeader from './header';
  import MeScroll from 'base/scroll';
  import HomeNav from './nav';
  import HomeRecommend from './recommend';
  import {HEADER_TRANSITION_HEIGHT} from './config';
  export default {
    name: 'Home',
    components: {
    	HomeHeader,
      HomeSlider,
      MeScroll,
      HomeRecommend,
      HomeNav,
      MeBacktop
    },
    data(){
      return {
      recommends: [],
      isBacktopVisble:false,
      isHeaderTransition:false
    };
    },
    // created() {

    // },
    methods: {
      pullDownTransitionEnd() {
        this.$refs.header.show();
      },
      scroll(translate) {
        this.changeHeaderStatus(translate);
      },
      updateScroll() {
      },
      getRecommends(recommends) {
        this.recommends = recommends;
      },
      pullToRefresh(end) {
        this.$refs.slider.update().then(end);
      },
      pullToLoadMore(end) {
      //   setTimeout(()=>{
      //   console.log('上拉刷新');
      //   console.log('end前');
      //   end();
      //   console.log('end后');
      // }, 1000);
        this.$refs.recommend.updata().then(end).catch(err => {
          console.log(err);
        end();
        });

      },
      scrollEnd(translate, scroll, pulling) {
        if(!pulling) {
          this.changeHeaderStatus(translate);
        }
        this.isBacktopVisble = translate < 0 && -translate > scroll.height;
      },
      backToTop() {
        this.$refs.scroll && this.$refs.scroll.scrollToTop();
      },
      changeHeaderStatus(translate) {
        if (translate>0) {
          this.$refs.header.hide();
          return;
        }
        this.$refs.header.show();
        this.isHeaderTransition = -translate > HEADER_TRANSITION_HEIGHT;
      }
    }

  };
</script>

<style lang="scss" scoped>
  @import '~assets/scss/mixins';

  .home {
  	overflow: hidden;
  	width: 100%;
  	height: 100%;
  	background-color: $bgc-theme;
  }
</style>
