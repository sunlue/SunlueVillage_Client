<template>
  <div class="home">
    <section class="home-banner">
      <publicHead :show="show" :rightBtn="rightBtn"/>
      <div class="banner-img">
        <iframe width="100%" height="100%" :src="iframeArr[iframeArrIndex]">
        </iframe>
        <div class="iframe-box-bg"></div>
      </div>
      <div class="banner-text">
        <img src="../../static/images/homeBnanerText.png" class="img-fluid" alt="">
      </div>

      <div class="search">
        <div class="search-box">
          <input type="text" v-on:keyup.enter="submitData" v-model="searchData" class="search-input"
                 placeholder="请输入关键词">
          <router-link class="search-btn" :to="{ path: 'search', query: { antistop: encodeURIComponent(this.searchData) }}"><img
            src="../../static/images/searchBtn.png"></router-link>
        </div>
        <a href="/map" class="village-address">村落分布</a>
      </div>

      <div class="hot-list-box">
        <span class="hot-name">热门</span>
        <a class="hot-list" v-for="item in hot" href="/village">
          {{ item.name }}
        </a>
      </div>

      <ul class="list-inline classify-box">
        <li v-for="item in classify" class="list-inline-item classify-list">
          <a href="">
            <i class="list-icon"></i>
            <span class="list-text">
            {{item}}
          </span>
          </a>
        </li>
      </ul>

      <span class="home-mouse-btn"></span>
    </section>

    <section class="home-hot common-hot-box">
      <p class="hot-village-title"><img src="../../static/images/hotVillageTitle.png" alt=""></p>

      <div class="container">
        <div class="row">
          <div class="hot-list" v-for="(item,index) in hot" v-if="index <= 8"
               :class="{'col-lg-6':index === 0 ,'col-lg-3':index > 0 }">
            <div class="box">
              <div class="img-box">
                <router-link class="search-btn"
                             :to="{ path: 'village-home', query: { vid: item.id }}">
                  <img v-bind:src="item.imgUrl" alt="" :title="item.name">
                </router-link>
              </div>
              <div class="text-box">
                <p class="clearfix">
                  <a href="">
                    <span class="name">{{item.name}}</span>
                  </a>
                  <span class="address">{{item.address}}</span>
                </p>
                <p class="type-box">
                  <span class="type" v-for="list in item.villageType">
                    {{list}}
                  </span>
                </p>
                <p class="clearfix">
                  <span class="look">{{item.look}}</span>
                  <span class="comment">{{item.comment}}</span>
                  <span class="like" :class="{'likeActive':hot[index].isLike===true}" @click="$myfunction.setLike(hot,item.id,)">{{item.like}}</span>

                </p>
              </div>
            </div>
          </div>
        </div>

        <a class="more" href="/village">更多</a>
      </div>
    </section>
    <section class="home-news">
      <p class="news-title"><img src="../../static/images/homeNewsTitle.png" alt=""></p>

      <div class="container">
        <div class="row">
          <div class="col-lg-6">
            <div class="top-news">
              <a href="">
                <p class="name">{{topNews.name}}</p>
                <p class="intro">{{topNews.intro}}</p>
              </a>
            </div>
            <div class="news-box">
              <p class="news-list" v-for="(list,index) in newsList">
                <a href="" class="clearfix">
                  <span class="name" :title="list.name">{{list.name}}</span>
                  <span class="date">{{list.date}}</span>
                </a>
              </p>
            </div>
          </div>
          <div class="col-lg-6">
            <div class="swiper-container" id="newsSwiper">
              <div class="swiper-wrapper">
                <div v-for="(list,index) in newsList" v-if="index<3" class="swiper-slide swiper-list">
                  <a href="">
                    <div class="img-box">
                      <img :src="list.imgUrl" alt="" :title="list.name">
                    </div>
                  </a>
                </div>
              </div>
              <!-- 如果需要分页器 -->
              <div class="swiper-pagination"></div>
              <div class="bottom-name">
                {{activeNews}}
              </div>
            </div>
          </div>
        </div>

        <div class="row home-infomation ">
          <div class="col-lg-4 home-infomation-list" v-for="(list,index) in infomationList" v-if="index<3">
            <div class="img-box">
              <a href="">
                <img :src="list.imgUrl" :alt="list.name" :title="list.name">
              </a>
            </div>
          </div>
        </div>

        <a class="more" href="/information-home">更多</a>
      </div>
    </section>

    <publicFooter/>
  </div>
</template>

<script>
    import publicHead from "../components/publicHead"
    import publicFooter from "../components/publicFooter"
    import axios from "axios"
    import Swiper from "swiper"
    import "swiper/css/swiper.min.css"

    export default {
        name: "home",
        components: {
            publicHead,
            publicFooter
        },
        data() {
            return {
                show: 1,
                searchData: '',
                activeNews: '',
                rightBtn: false,
                hot: [],
                classify: ['土货', '农场', '康养', '景点', '民俗', '民宿'],
                topNews: [],
                newsList: [],
                infomationList: [],
                iframeArr: ['../../static/krpano/index.html', '../../static/krpano/index1.html', '../../static/krpano/index2.html', '../../static/krpano/index3.html', '../../static/krpano/index4.html'],
                iframeArrIndex: ''
            }
        },
        mounted: function () {
            const self = this;
            this.getHotVillage();
            this.getTopNews();
            this.getNews();
            this.getInformation();
            this.iframeArrIndex = ~~(Math.random() * this.iframeArr.length);
            window.addEventListener('scroll', this.getRightBtnPosition, false);
            this.initNewsSwiper();
        },
        destroyed: function () {
            window.removeEventListener('scroll', this.getRightBtnPosition, false)
        },
        methods: {
            // 提交搜索数据
            submitData: function () {
                this.$router.push({path: 'search', query: {antistop: encodeURIComponent(this.searchData)}});
            },
            // 获取热门村庄
            getHotVillage: function () {
                let self = this
                axios.get('../../static/data/tuijiancunzhuang.json').then(function (res) {
                    self.hot = res.data
                })
            },
            // 获取滑动条位置，设置右侧导航颜色
            getRightBtnPosition: function () {
                let top = document.documentElement.scrollTop || document.body.scrollTop;
                let screenH = document.documentElement.clientHeight / 2;
                if (top > screenH) {
                    this.rightBtn = true
                } else {
                    this.rightBtn = false
                }
            },
            // 获取置顶的一条活动资讯
            getTopNews: function () {
                let self = this
                axios.get('../../static/data/tuijianzhidingwenzhang.json').then(function (res) {
                    self.topNews = res.data[0]
                })
            },
            // 获取新闻列表
            getNews: function () {
                let self = this
                axios.get('../../static/data/homeNews.json').then(function (res) {
                    self.newsList = res.data
                    self.activeNews = res.data[0].name
                })
            },
            // 获取资讯
            getInformation: function () {
                let self = this
                axios.get('../../static/data/homezixun.json').then(function (res) {
                    self.infomationList = res.data
                })
            },
            // 初始化新闻动态swiper
            initNewsSwiper(){
                let self = this;
                new Swiper('#newsSwiper', {
                    autoplay: {
                        delay: 3000,
                        stopOnLastSlide: false,
                        disableOnInteraction: true,
                    },
                    pagination: {
                        el: '.swiper-pagination',
                        clickable: true
                    },
                    observer: true,
                    on: {
                        slideChange: function () {
                            let index = this.realIndex;
                            self.activeNews = self.newsList[index].name;
                        }
                    },
                })
            }
        }
    }

</script>

<style src="../assets/css/home.css" scoped>
</style>

<style>
  .home-news #newsSwiper .swiper-pagination .swiper-pagination-bullet {
    width: 11px;
    height: 10px;
    opacity: 1;
    background-color: #fff;
    margin: 0 9px;
    border-radius: 10px;
    transition: .3s;
  }

  .home-news #newsSwiper .swiper-pagination .swiper-pagination-bullet-active {
    width: 38px;
    background-color: #37cf9f;
  }
</style>
