<template>
  <div class="recommend">
    <h3 class="recommend-title">热卖推荐</h3>
    <div class="loading-container" v-if="!recommends.length">
      <me-loading inline/>
    </div>
    <ul class="recommend-list" v-else>
       <!--  <li class="recommend-item">
            <router-link
             class="recommend-link"
          :to="{name: 'home-product', params: {id: 527981440603}}"
        >
                  <p class="recommend-pic"><img class="recommend-img" src="//gju4.alicdn.com/tps/i2/O1CN01fIsAVM1GSSUiiiWEt_!!0-juitemmedia.jpg"></p>
                  <p class="recommend-name">买一送一 共2双 高弹力男女防晒袖</p>
                  <p class="recommend-origPrice"><del>¥50.00</del></p>
                  <p class="recommend-info">
                    <span class="recommend-price">¥<strong class="recommend-price-num">9.8</strong></span>
                    <span class="recommend-count">4911件已售</span>
                   </p>
                </router-link>
         </li>
 -->
         <li
        class="recommend-item"
        v-for="(item, index) in recommends"
        :key="index"
      >
        <router-link
          class="recommend-link"
          :to="{name: 'home-product', params: {id: item.baseinfo.itemId}}"
        >
          <p class="recommend-pic"><img class="recommend-img" v-lazy="item.baseinfo.picUrlNew"></p>
          <p class="recommend-name">{{item.name.shortName}}</p>
          <p class="recommend-origPrice"><del>¥{{item.price.origPrice}}</del></p>
          <p class="recommend-info">
            <span class="recommend-price">¥<strong class="recommend-price-num">{{item.price.actPrice}}</strong></span>
            <span class="recommend-count">{{item.remind.soldCount}}件已售</span>
          </p>
        </router-link>
      </li>


    </ul>
  </div>
</template>

<script>
  import {getHomeRecommend} from 'api/home';
  import MeLoading from 'base/loading';
  export default {
    name: 'HomeRecommend',
    components: {
      MeLoading
    },
    data() {
    	return {
    		recommends: [],
    		curPage: 1,
    		totalPage: 1
    	};
    },
    created() {
    	this.getRecommend();
    },
    methods: {
      // api
      updata() {
        return this.getRecommend();
      },

    	getRecommend(){
    	  if (this.curPage > this.totalPage) {
    	  	return Promise.reject(new Error('没有更多了'));
    	  }
    	  return getHomeRecommend(this.curPage).then(data => {
            // this.recommends = data.itemList;
            // console.log(this.recommends);
            // console.log(this.recommends[0].baseinfo.itemId);
              // item = this.recommends[0]
              // console.log(item.baseinfo.itemId);
              // console.log(this.recommends[0].baseinfo.picUrlNew);
              // console.log(this.recommends[0].name.shortName);
              // console.log(this.recommends[0].price.origPrice);
              // console.log(this.recommends[0].price.actPrice);
              // console.log(this.recommends[0].remind.soldCount);
    	  		// console.log(data);
            return new Promise(
              resolve => {
              if (data) {
                this.curpage++;
                this.totalPage = data.totalPage;
                this.recommends = this.recommends.concat(data.itemList);
                this.$emit('loaded', this.recommends);
                resolve();     
    	  	}
    	  });
    	});
    }
  }
  };
</script>

<style lang="scss" scoped>
  @import "~assets/scss/mixins";

  .recommend {
    &-title {
      position: relative;
      width: 100%;
      padding: 10px 0;
      font-size: $font-size-l;
      text-align: center;

      &:before,
      &:after {
        content: '';
        position: absolute;
        top: 50%;
        width: 40%;
        height: 1px;
        background-color: #ddd;
      }
      &:before {
        left: 0;
      }
      &:after {
        right: 0;
      }
    }

    &-list {
      @include flex-between();
      flex-wrap: wrap;
    }

    &-item {
      width: 49%;
      background-color: #fff;
      box-shadow: 0 1px 1px 0 rgba(0, 0, 0, 0.12);
      margin-bottom: 8px;
    }
    &-span {
      display: inline-block;
      height: 150px;
      width: 150px;
      border:solid  1px red;
    }

    &-link {
      display: block;
    }

    &-pic {
      position: relative;
      width: 100%;
      padding-top: 100%;
      margin-bottom: 5px;
    }

    &-img {
      position: absolute;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
    }

    &-name {
      height: 36px;
      padding: 0 5px;
      margin-bottom: 8px;
      line-height: 1.5;
      @include multiline-ellipsis();
    }

    &-origPrice {
      padding: 0 5px;
      margin-bottom: 8px;
      color: #ccc;
    }

    &-info {
      @include flex-between();
      padding: 0 5px;
      margin-bottom: 8px;
    }

    &-price {
      color: #e61414;
    }

    &-price-num {
      font-size: 20px;
    }

    &-count {
      color: #999;
    }
  }

  .loading-container {
    padding-top: 100px;
  }

</style>
