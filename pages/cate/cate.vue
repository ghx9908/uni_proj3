<template>
  <view>
    <!-- <my-search :bgcolor="'pink'"></my-search> -->
    <my-search @click="gotoSearch"></my-search>
    <view class="scroll-view-container">
      <scroll-view class="left-scroll-view" scroll-y :style="{height: wh+'px'}">

        <block v-for="(item,i) in cateList" :key="i">
          <view @click="activeChange(i)" :class="['left-scroll-view-item',i===active ? 'active' : '']">{{item.cat_name}}</view>
        </block>
      </scroll-view>
      <scroll-view :scrollTop="scrollTop" class="right-scroll-view" scroll-y :style="{height: wh+'px'}">
        <view class="cate-lv2" v-for="(item2,i2) in cateLevel2" :key="i2">
          <view class="cate-lv2-title">
            /{{item2.cat_name}}/
          </view>
          <view class="cate-lv3-list">
            <view class="cate-lv3-item" v-for="(item3,i3) in item2.children" :key="i3" @click="gotoGoodsList(item3)">
              <image :src="item3.cat_icon">

              </image>
              <text>
                {{item3.cat_name}}
              </text>
            </view>
          </view>

        </view>


      </scroll-view>
    </view>
  </view>
</template>

<script>
  // 导入自己封装的 mixin 模块
  import badgeMix from '@/mixins/tabbar-badge.js'
  export default {
    // 将 badgeMix 混入到当前的页面中进行使用
    mixins: [badgeMix],

    data() {
      return {
        wh: 0,
        cateList: [],
        active: 0,
        cateLevel2: [],
        scrollTop: 0
      };
    },
    onLoad() {

      const sysInfo = uni.getSystemInfoSync()
      this.wh = sysInfo.windowHeight - 50
      // console.log(sysInfo)
      this.getCateList()
    },
    methods: {
      async getCateList() {
        const {
          data: res
        } = await uni.$http.get('/api/public/v1/categories')
        console.log(res)
        if (res.meta.status !== 200) return uni.$showMsg()
        // uni.hideLoading()
        this.cateList = res.message
        this.cateLevel2 = res.message[0].children
      },
      activeChange(i) {
        this.active = i
        this.cateLevel2 = this.cateList[i].children;
        this.scrollTop = this.scrollTop ? 0 : 1
      },
      gotoGoodsList(item3) {
        // console.log(1)
        uni.navigateTo({
          url: '/subpkg/goods_list/goods_list?cid=' + item3.cat_id
        })
      },
      gotoSearch() {
        // console.log(1)
        uni.navigateTo({
          url: '/subpkg/search/search'
        })
      },
      // setBadge() {
      //   uni.setTabBarBadge({
      //     index:2,
      //     text:this.total+''
      //   })
      // }
    },
    // computed:{
    //   ...mapGetters('m_cart',['total'])
    // },
    // onShow() {
    //   this.setBadge()
    // }
  }
</script>

<style lang="scss">
  .scroll-view-container {
    display: flex;

    .left-scroll-view {
      width: 120px;

      .left-scroll-view-item {
        line-height: 60px;
        background-color: #f7f7f7;
        text-align: center;
        font-size: 12px;

        &.active {
          background-color: #fff;
          position: relative;

          &::before {
            content: '';
            display: block;
            width: 3px;
            height: 30px;
            position: absolute;
            top: 50%;
            left: 0;
            transform: translateY(-50%);
            background-color: #c00000;

          }
        }


      }
    }
  }

  .cate-lv2-title {
    font-size: 12px;
    font-weight: bold;
    text-align: center;
    padding: 15px 0;
  }

  .cate-lv3-list {
    display: flex;
    flex-wrap: wrap;

    .cate-lv3-item {
      display: flex;
      flex-direction: column;
      align-items: center;
      margin-bottom: 10px;
      width: 33.33%;

      image {
        width: 60px;
        height: 60px;
      }

      text {
        font-size: 12px;
      }
    }
  }
</style>
