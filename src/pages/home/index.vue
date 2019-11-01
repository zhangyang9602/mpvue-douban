<template>
  <div v-if="categoryList.length>0">
    <div class="header">
      <span>豆瓣</span>
      <icon type="search"
            size="18"
            color="#00b600" />
      <button class="btn">打开豆瓣</button>
    </div>
    <div class="main"
         
         v-for="(category,k) in categoryList"
         :key='k'>
      <div class="title">
        <span class="hotmoive">{{category.name}}</span>
        <span class="more">更多</span>
      </div>
      <scroll-view class="scroll-view_H"
                   scroll-x="true"
                   bindscroll="scroll"
                   style="width: 100%">
        <view id="demo1"
              class="scroll-view-item_H demo-text-1"
              v-for="items in category.list"
              :key="items.id">
          <img :src="items.images.medium"
               alt="">
          <div class="rating">
            <p>{{items.title}}</p>
            <block v-for="(itemss,i) in items.scoreNum"
                   :key="i">
              <img src="../../../static/images/star.svg"
                   alt="">
            </block>
            <block v-if="items.scoreNum>0"
                   v-for="(itemss,i) in 5-items.scoreNum"
                   :key="i">
              <img src="../../../static/images/unstar.svg"
                   alt="">
            </block>
            <span class="comment">{{items.rating.average>0?items.rating.average:'暂无评论'}}</span>
          </div>
        </view>

      </scroll-view>
    </div>
  </div>

</template>

<script>
export default {
  data () {
    return {
      categoryList: [
        { name: '影院热映',
          param: 'in_theaters',
          list: []
        },
        { name: '免费在线观影',
          param: 'top250',
          list: []
        }
      ]
    }
  },
  created () {
    this.categoryList.forEach(item => {
      this.getmovieList(item)
    })
  },
  methods: {
    getmovieList (item) {
      this.$request({
        url: `https://api.douban.com/v2/movie/${item.param}?apikey=0df993c66c0c636e29ecbb5344252a4a`,
        method: 'get',
        header: {
          'content-type': 'application/x-www-form-urlencoded'
        }
      }).then(res => {
        if (res.statusCode === 200) {
          const movieList = res.data.subjects
          movieList.forEach(v => {
            v.scoreNum = Math.ceil(v.rating.average / 2)
          })
          console.log(movieList)
          item.list = movieList
        }
      })
    }
  }
}
</script>

<style>
.header {
  display: flex;
  align-items: center;
  height: 94rpx;
  border-bottom: 1px solid #eee;
  padding: 0 36px;
}
icon {
  flex: 1;
}
.header span {
  font-size: 18px;
  color: #00b600;
  font-weight: bold;
  margin-right: 28rpx;
}
button {
  width: 200rpx;
  height: 58rpx;
  background-color: #42bd56;
  color: #fff;
  line-height: 58rpx;
  text-align: center;
}
.title {
  display: flex;
  justify-content: space-between;
  padding: 28rpx 38px;
}
.more {
  color: #42bd56;
  font-size: 20px;
}
.hotmoive {
  font-size: 20px;
}
.scroll-view_H {
  white-space: nowrap;
  padding-left: 38rpx;
}
.scroll-view-item_H {
  display: inline-block;
  width: 200rpx;
  height: 286rpx;
  margin-right: 18rpx;
}
.scroll-view-item_H img {
  width: 100%;
  height: 100%;
  display: inline-block;
}
.rating {
  text-align: center;
}
.rating p {
  font-size: 16px;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}
.rating img {
  width: 20rpx;
  height: 20rpx;
}
.comment {
  margin-left: 4px;
  color: #aaa;
}
</style>