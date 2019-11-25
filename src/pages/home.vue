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
          <router-link class="search-btn"
                       :to="{ path: 'search', query: { antistop: encodeURIComponent(this.searchData) }}"><img
            src="../../static/images/searchBtn.png"></router-link>
        </div>
        <a href="/map" class="village-address">村落分布</a>
      </div>

      <div class="hot-list-box">
        <a href="/village" class="hot-name">热门</a>
        <a class="hot-list" v-for="item in hot" :href="'/village-home?vid='+item.uniqid">
          {{ item.name }}
        </a>
      </div>

      <ul class="list-inline classify-box">
        <li v-for="(item,index) in classify" class="list-inline-item classify-list">
          <a :href="'/village?cid='+index">
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
               :class="{'col-lg-6':index === 0 ,'col-lg-3 col-sm-6':index > 0 }">
            <div class="box">
              <div class="img-box">
                <a :href="'village-home?vid='+item.uniqid">
                  <img :src="$config.apiUrl + item.thumbnail" alt="" :title="item.name">
                </a>
              </div>
              <div class="text-box">
                <p class="clearfix">
                  <a :href="'village-home?vid='+item.uniqid">
                    <span class="name">{{item.name}}</span>
                  </a>
                  <span class="address">{{item.town_text}}</span>
                </p>
                <p class="type-box">
                  <span class="type" v-for="list in item.type">
                    {{list}}
                  </span>
                </p>
                <p class="clearfix">
                  <span class="look">{{item.hits}}</span>
                  <!--                  <span class="comment">{{item.comment}}</span>-->
                  <span class="like" :class="{'likeActive':hot[index].isLike===true}"
                        @click="setLike(hot,item.uniqid)">{{item.like}}</span>

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
        <div class="row flex-row-reverse">
          <div class="col-lg-6">
            <div class="swiper-container" id="newsSwiper">
              <div class="swiper-wrapper">
                <div v-for="(list,index) in newsRecommendedList" v-if="list.thumbnail" class="swiper-slide swiper-list">
                  <a :href="'/article?id='+list.uniqid">
                    <div class="img-box">
                      <img :src="$config.apiUrl + list.thumbnail" alt="" :title="list.title">
                    </div>
                    <div class="bottom-name">
                      {{list.title}}
                    </div>
                  </a>
                </div>
              </div>
              <!-- 如果需要分页器 -->
              <div class="swiper-pagination"></div>

            </div>
          </div>
          <div class="col-lg-6">
            <div class="top-news">
              <a :href="'/article?id='+list.uniqid" v-for="(list,index) in newsTopList">
                <p class="name">{{list.title}}</p>
                <p class="intro">{{list.excerpt}}</p>
              </a>
            </div>
            <div class="news-box">
              <p class="news-list" v-for="(list,index) in newsList">
                <a :href="'/article?id='+list.uniqid" class="clearfix">
                  <span class="name" :title="list.title">{{list.title}}</span>
                  <span class="date">{{list.create_time | formatDateYMD}}</span>
                </a>
              </p>
            </div>
          </div>
        </div>

        <div class="row home-infomation">
          <div class="col-lg-4 col-sm-6 home-infomation-list" v-for="(list,index) in infomationList">
            <div class="img-box">
              <a :href="'/article?id='+list.uniqid">
                <img :src="$config.apiUrl + list.thumbnail" :alt="list.title" :title="list.title">
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
                // activeNews: '',
                rightBtn: false,
                hot: [],
                informationType: "ARTICLE-TYPE-5D1C482F4320B",
                newsType: "ARTICLE-TYPE-5DD3A76B30129",
                classify: ['土货', '农场', '康养', '景点', '民俗', '民宿'],
                newsList: [],
                newsRecommendedList: [],
                newsTopList: [],
                infomationList: [],
                iframeArr: ['../../static/krpano/index.html', '../../static/krpano/index1.html', '../../static/krpano/index2.html', '../../static/krpano/index3.html', '../../static/krpano/index4.html'],
                iframeArrIndex: ''
            }
        },
        mounted: function () {
            const self = this;
            this.getHotVillage();
            // this.getTopNews();
            this.getRecommendedNews();
            this.getNews();
            this.getTopNews();
            this.getInformation();
            this.iframeArrIndex = ~~(Math.random() * this.iframeArr.length);
            window.addEventListener('scroll', this.getRightBtnPosition, false);
        },
        destroyed: function () {
            window.removeEventListener('scroll', this.getRightBtnPosition, false)
        },
        methods: {
            // 点赞
            setLike: function (data, uniqid) {
                console.log(uniqid)
                for (let i = 0; i < data.length; i++) {
                    if (uniqid === data[i].uniqid) {
                        if (!data[i].isLike) {
                            axios.put(this.$config.apiUrl + "village/data/like", {
                                uniqid: uniqid
                            }).then(res => {
                                console.log(res)
                                if (res.data.code === 200) {
                                    data[i].like = parseInt(data[i].like) + 1;
                                    data[i].isLike = true
                                }
                            })
                        }
                    }
                }
            },
            // 提交搜索数据
            submitData: function () {
                this.$router.push({path: 'search', query: {antistop: encodeURIComponent(this.searchData)}});
            },
            // 获取热门村庄
            getHotVillage: function () {
                // let self = this
                // axios.get('../../static/data/tuijiancunzhuang.json').then(function (res) {
                //     self.hot = res.data
                // })
                let apiUrl = this.$config.apiUrl + 'village/data/read';
                axios.get(apiUrl, {
                    params: {
                        limit: 7,
                        page: 1,
                        hot: 1
                    }
                }).then(res => {
                    if (res.data.code === 200) {
                        for (let i = 0; i < res.data.data.data.length; i++) {
                            res.data.data.data[i].isLike = false
                        }
                        this.hot = res.data.data.data
                    }
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

            // 获取新闻列表
            getNews: function () {
                // let self = this
                // axios.get('../../static/data/homeNews.json').then(function (res) {
                //     self.newsList = res.data
                //     self.activeNews = res.data[0].name
                // })
                let apiUrl = this.$config.apiUrl + 'portal/article/data/read';
                axios.get(apiUrl, {
                    params: {
                        type: this.newsType,
                        page: 1,
                        limit: 7,
                        hot: 1,
                    }
                }).then((res) => {
                    if (res.data.code === 200) {
                        this.newsList = res.data.data.data;
                    }
                })
            },
            // 获取置顶新闻列表
            getTopNews: function () {
                let apiUrl = this.$config.apiUrl + 'portal/article/data/read';
                axios.get(apiUrl, {
                    params: {
                        type: this.newsType,
                        page: 1,
                        limit: 1,
                        is_top: 1,
                    }
                }).then((res) => {
                    if (res.data.code === 200) {
                        this.newsTopList = res.data.data.data;
                    }
                })
            },
            // 获取新闻轮播图
            getRecommendedNews: function () {
                let apiUrl = this.$config.apiUrl + 'portal/article/data/read';
                axios.get(apiUrl, {
                    params: {
                        type: this.newsType,
                        page: 1,
                        limit: 5,
                        recommended: 1,
                    }
                }).then((res) => {
                    if (res.data.code === 200) {
                        this.newsRecommendedList = res.data.data.data;
                        this.$nextTick(() => {
                            this.initNewsSwiper();
                        })
                    }
                })
            },
            // 获取活动
            getInformation: function () {
                // let self = this
                // axios.get('../../static/data/homezixun.json').then(function (res) {
                //     self.infomationList = res.data
                // })

                let apiUrl = this.$config.apiUrl + 'portal/article/data/read';
                axios.get(apiUrl, {
                    params: {
                        type: this.informationType,
                        page: 1,
                        limit: 3,
                        recommended: 1
                    }
                }).then(res => {
                    if (res.data.code === 200) {
                        this.infomationList = res.data.data.data;
                    }
                })
            },
            // 初始化新闻动态swiper
            initNewsSwiper() {
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
                            self.activeNews = self.newsRecommendedList[index].title;
                        }
                    },
                })
            }
        },
        filters: {
            formatDateYMD: function (value) {
                let date = new Date(value);
                let y = date.getFullYear();
                let MM = date.getMonth() + 1;
                MM = MM < 10 ? ('0' + MM) : MM;
                let d = date.getDate();
                d = d < 10 ? ('0' + d) : d;
                return y + '-' + MM + '-' + d;
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
