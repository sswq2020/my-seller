<template>
    <div class="seller" ref="sellerWrapper">
        <div class="seller-content">
            <div class="overview">
                <div class="title" >{{seller.name}}</div>
                <div class="desc">
                    <v_star :size="36" :score="seller.score"></v_star>
                    <div class="text">({{seller.ratingCount}})</div>
                    <div class="text">月售{{seller.sellCount}}单</div>
                </div>
                <ul class="remark">
                    <li class="block">
                        <h2>起送价</h2>
                        <div class="content">
                            {{seller.minPrice}}<span class="stress">元</span>
                        </div>
                    </li>
                    <li class="block">
                        <h2>商家配送</h2>
                        <div class="content">
                            {{seller.deliveryPrice}}<span class="stress">元</span>
                        </div>
                    </li>
                    <li class="block">
                        <h2>平均配送时间</h2>
                        <div class="content">
                            {{seller.deliveryTime}}<span class="stress">分钟</span>
                        </div>
                    </li>
                </ul>
                <div class="favorite" @click="toggleFavorite">
                    <span class="icon-favorite" :class="{'active':favorite}"></span>
                    <span class="text">{{favoriteText}}</span>
                </div>
            </div>
            <split></split>
            <div class="bulletin">
                <h1 class="title">公告与活动</h1>
                <div class="content-wrapper border-1px">
                    <p class="content">{{seller.bulletin}}</p>
                </div>
                <ul v-if="seller.supports" class="supports">
                    <li class="support-item border-1px" v-for="(item,index) in seller.supports" :key="index">
                        <span class="icon" :class="classMap[seller.supports[index].type]"></span>
                        <span class="text">{{seller.supports[index].description}}</span>
                    </li>
                </ul>
            </div>
            <split></split>
            <div class="pics">
               <h1 class="title">商家实景</h1> 
                <div class="pic-wrapper" ref="picWrapper">
                    <ul class="pic-list">
                        <li class="pic-item" v-for="(pic,index) in seller.pics" :key="index">
                            <img :src="pic" width="120" height="90" alt="">
                        </li>
                    </ul>
                </div>
            </div>
            <split></split>
            <div class="info">
                <div class="title border-1px">商家信息</div>
                <ul>
                    <li class="info-item border-1px" v-for="(info,index) in seller.infos" :key="index">{{info}}</li>
                </ul>
            </div>
        </div>
    </div>
</template>

<script type="text/ecmascript-6">
import star from 'components/star/star.vue'
import split from 'components/split/split.vue'
import BScroll from 'better-scroll'
import {saveToLocal, loadFromLocal} from 'common/js/store'
export default {
  props: {
    seller: { type: Object }
  },
  data () {
      return {
          favorite: (() => {
              return loadFromLocal(this.seller.id, 'favorite', false)
          })()
      }
  },
  components: {
    v_star: star,
    split: split
  },
  created () {
      this.classMap = ['decrease', 'discount', 'guarantee', 'invoice', 'special']
  },
  computed: {
      favoriteText () {
          return this.favorite ? '已收藏' : '收藏'
      }
  },
  mounted () {
      let vm = this;
      console.log(11);
      vm.$nextTick(() => {
          vm._initScroll();
          vm._computedWidth();
          vm.$nextTick(() => {
             vm._picScroll();
          })
      })
  },
  methods: {
      _initScroll () {
          let vm = this;
          if (!vm.scroll) {
            vm.scroll = new BScroll(vm.$refs.sellerWrapper, {
                click: true
            })
          } else {
              vm.scroll.refresh();
          }
      },
      _picScroll () {
          let vm = this;
          if (!vm.picScroll) {
              vm.picScroll = new BScroll(vm.$refs.picWrapper, {
                scrollX: true,
                probeType: 2,
                click: true
              })
          } else {
              vm.picScroll.refresh();
          }
      },
      _computedWidth () {
          let arrwidth = 0;
          let picItems = this.$refs.picWrapper.getElementsByClassName('pic-item')
          let piclist = this.$refs.picWrapper.getElementsByClassName('pic-list')[0]
          for (let i = 0; i < picItems.length; i++) {
              arrwidth = arrwidth + picItems[i].offsetWidth + 3
          }
          piclist.style.width = arrwidth + 'px';
      },
      toggleFavorite (e) {
        if (!event._constructed) {
          return;
        }
        this.favorite = !this.favorite
        saveToLocal(this.seller.id, 'favorite', this.favorite)
      }
  },
  watch: {
      seller () {
          let vm = this;
          vm.$nextTick(() => {
              vm._initScroll();
              vm._computedWidth();
              vm.$nextTick(() => {
                  vm._picScroll();
              })
          })
      }
  }
};
</script>

<style lang="stylus" rel="stylesheet/stylus">
@import"../../common/stylus/mixin.styl"
  .seller
    position:absolute
    top:174px
    bottom:0
    left:0
    width:100%
    overflow:hidden
    .overview
      position:relative
      padding:18px
      .title
        margin-bottom:8px
        line-height:14px
        font-size:14px
        color:rgb(7,17,27)
      .desc
        padding-bottom:18px
        border-1px(rgba(7,17,27,0.1))
        font-size:0
        .star
          display:inline-block
          vertical-align:top
          margin-right:8px
        .text
          display:inline-block
          margin-right:12px
          line-height:18px
          font-size:10px
          color:rgb(77,85,93)
      .remark
        display:flex
        padding-top:18px
        .block
          flex:1
          text-align:center
          border-right:1px solid rgba(7,17,27,0.1)
          &:last-child
            border:none
          h2
            margin-bottom:4px
            line-height:10px
            font-size:10px
            color:rgb(147, 153, 159)
          .content
            line-height:24px
            font-size:24px
            font-weight:200
            color:rgb(7,17,27)
            .stress
              font-size:10px
      .favorite
        position:absolute
        right:11px
        top:18px
        text-align:center
        .icon-favorite
          display:block
          margin-bottom:4px
          line-line-height:24px
          font-size:24px
          color:#d4d6d9
          &.active
            color:rgb(240, 20, 20)
        .text
          line-height:10px
          font-size:10px
          color:rgb(77, 85, 93)
    .bulletin
      padding:18px 18px 0 18px
      .title
        margin-bottom:8px
        line-height:14px
        font-size:14px
        color:rgb(7,17,27)
      .content-wrapper
        padding:0 12px 16px 12px
        border-1px(rgba(7,17,27,0.1))
        .content
          line-height:24px
          font-size:12px
          color:rgb(240,20,20)
      .supports
        .support-item
          padding:16px 12px
          border-1px(rgba(7,17,27,0.1))
          font-size:0
          &:last-child
            border-none()
          .icon
             display:inline-block
             margin-right:6px
             vertical-align:top
             width:16px
             height:16px
             background-size:16px 16px
             background-repeat:no-repeat
             &.decrease
               bg-image('decrease_4')
             &.discount
               bg-image('discount_4')            
             &.guarantee
               bg-image('guarantee_4')            
             &.invoice
               bg-image('invoice_4')            
             &.special
               bg-image('special_4')
          .text
            display:inline-block
            vertical-align:top
            font-size:12px
            line-height:16px
            font-weight:200
            color:rgb(7,17,27)
    .pics
      padding:18px
      .title
        margin-bottom:12px
        line-height:14px
        font-size:14px
        color:rgb(7,17,27)
      .pic-wrapper
        width:100%;
        overflow:hidden
        white-space:nowrap // 不会折行
        .pic-list
          font-size:0
          .pic-item
            display:inline-block
            margin-right:6px
            width:120px
            height:90px
            &:last-child
              margin-right:0
    .info
      padding:18px 18px 0 18px
      .title
        padding-bottom:12px
        line-height:14px
        border-1px(rgba(7,17,27,0.1))
        color:rgb(7,17,27,0.1)
        font-size:14px
      .info-item
        padding:16px 12px
        line-height:16px
        border-1px(rgba(7,17,27,0.1))
        color:rgb(7,17,27,0.1)
        font-size:12px
        &:last-child
          border-none()

</style>