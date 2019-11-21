<template>
  <div id="informationHome" :class="{'inside-box':show != 1}">
    <publicHead :show="show" :rightBtn="true"/>
    <section class="information-home-banner">
      <div class="swiper-container information-banner">
        <div class="swiper-wrapper">
          <div class="swiper-slide" v-for="item in banner">
            <div class="img-box">
              <img :src="item.imgUrl" alt="">
            </div>
          </div>
        </div>
      </div>
      <div class="swiper-pagination information-pagination"></div>
    </section>

    <section class="information-news-box">
      <p class="news-title"><img src="../../static/images/informationTitle.png" alt=""></p>

      <div class="container">
        <div class="row flex-row-reverse">
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
        </div>
        <a class="newsMore" href="/news">查看更多</a>

        <p class="news-title action-title"><img src="../../static/images/activity.png" alt=""></p>
        <div class="row home-infomation">
          <div class="col-lg-4 col-sm-6 home-infomation-list" v-for="list in infomationList">
            <div class="img-box">
              <a href="">
                <img :src="list.imgUrl" :alt="list.name" :title="list.name">
              </a>
            </div>
          </div>
        </div>

        <a class="actionMore" href="/action">查看更多</a>
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
        components: {
            publicHead,
            publicFooter
        },
        data() {
            return {
                show: 3,
                banner: [],
                newsList: [],
                activeNews: [],
                topNews: [],
                infomationList: []
            }
        },
        mounted() {
            this.getBanner();
            this.initNewsSwiper();
            this.getTopNews();
            this.getNews();
            this.getInformation();
        },
        methods: {
            // 获取banner
            getBanner() {
                axios.get("../../static/data/informationBanner.json", {}).then((res) => {
                    this.banner = res.data;
                    this.$nextTick(() => {
                        this.initBannerSwiper()
                    })
                })
            },
            // 获取新闻列表
            getNews: function () {
                axios.get('../../static/data/homeNews.json').then((res) => {
                    this.newsList = res.data
                    this.activeNews = res.data[0].name
                })
            },
            // 获取置顶的一条活动资讯
            getTopNews: function () {
                axios.get('../../static/data/tuijianzhidingwenzhang.json').then((res) => {
                    this.topNews = res.data[0]
                })
            },
            // 获取资讯
            getInformation: function () {
                let self = this
                axios.get('../../static/data/homezixun.json').then(function (res) {
                    self.infomationList = res.data
                })
            },
            // 初始化顶部swiper
            initBannerSwiper() {
                new Swiper('.information-banner', {
                    autoplay: {
                        delay: 4000,
                        stopOnLastSlide: false,
                        disableOnInteraction: false,
                    },
                    observer: true,
                    observeParents: true,
                    speed: 800,
                    spaceBetween: 0,
                    centeredSlides: true,
                    slidesPerView: 1.3,
                    watchSlidesProgress: true,
                    initialSlide: 1,
                    loop: true,
                    pagination: {
                        el: '.information-pagination',
                        clickable: true
                    },
                    on: {
                        setTranslate: function () {
                            let slides = this.slides
                            for (let i = 0; i < slides.length; i++) {
                                let slide = slides.eq(i)
                                let progress = slides[i].progress
                                slide.transform('scale(' + (1 - Math.abs(progress) / 8) + ')');
                            }
                        },
                        setTransition: function (transition) {
                            for (let i = 0; i < this.slides.length; i++) {
                                let slide = this.slides.eq(i)
                                slide.transition(transition);
                            }
                        },
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
                            self.activeNews = self.newsList[index].name;
                        }
                    },
                })
            }
        }
    }
</script>

<style src="../assets/css/infomationHome.css" scoped>
</style>
<style>
  .information-pagination {
    left: 0;
    right: 0;
    margin: auto;
    bottom: 26px;
  }

  .information-pagination .swiper-pagination-bullet {
    width: 12px;
    height: 12px;
    opacity: 0.64;
    border-radius: 6px;
    background-color: #666;
    margin: 0 10px;
  }

  .information-pagination .swiper-pagination-bullet-active {
    width: 44px;
    background-color: #37cf9f;
  }

  .information-news-box #newsSwiper .swiper-pagination .swiper-pagination-bullet {
    width: 11px;
    height: 10px;
    opacity: 1;
    background-color: #fff;
    margin: 0 9px;
    border-radius: 10px;
    transition: .3s;
  }

  .information-news-box #newsSwiper .swiper-pagination .swiper-pagination-bullet-active {
    width: 38px;
    background-color: #37cf9f;
  }
</style>
