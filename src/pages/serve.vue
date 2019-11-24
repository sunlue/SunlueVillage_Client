<template>
  <div id="serve" class="inside-box">
    <publicHead :show="show" :rightBtn="false"/>
    <div class="serve-banner-box">
      <img src="../../static/images/serveBanner.jpg" alt="" class="img-fluid">
    </div>
    <section class="serve-box">
      <div class="container-fluid">
        <div class="row">
          <div class="col-lg-4 list-box">
            <div class="swiper-container serve-swiper">
              <div class="swiper-wrapper">
                <div class="swiper-slide" v-for="item in recommendList">
                  <a :href="'/article?id='+item.uniqid">
                    <div class="img-box">
                      <img :src="$config.apiUrl + item.thumbnail" alt="" :title="item.title">
                    </div>
                    <p class="list-name" :title="item.title">{{item.title}}</p>
                  </a>
                </div>
              </div>
              <div class="swiper-pagination serve-pagination"></div>
            </div>
          </div>
          <div class="col-lg-5 list-box hot-box">
            <div class="box">
              <div class="hot-name">
                热门消息
              </div>
              <a :href="'/article?id='+item.uniqid" v-for="item in hotList">
                <p class="hot-list clearfix">
                  <span class="name">{{item.title}}</span>
                  <span class="date">{{item.create_time | formatDateMD}}</span>
                </p>
              </a>

            </div>
          </div>
          <div class="col-lg-3 list-box clearfix">
            <div class="icon-list">
              <a href="/serve-list?id=1">
                <div class="default">
                  <div class="icon-img"></div>
                  <p class="title">党宣</p>
                </div>
                <div class="hover">
                  <div class="icon-img"></div>
                  <p class="title">党宣</p>
                </div>
              </a>
            </div>
            <div class="icon-list">
              <a href="/serve-list?id=2">
                <div class="default">
                  <div class="icon-img"></div>
                  <p class="title">生产</p>
                </div>
                <div class="hover">
                  <div class="icon-img"></div>
                  <p class="title">生产</p>
                </div>
              </a>
            </div>
            <div class="icon-list">
              <a href="/serve-list?id=3">
                <div class="default">
                  <div class="icon-img"></div>
                  <p class="title">科技</p>
                </div>
                <div class="hover">
                  <div class="icon-img"></div>
                  <p class="title">科技</p>
                </div>
              </a>
            </div>
            <div class="icon-list">
              <a href="/serve-list?id=4">
                <div class="default">
                  <div class="icon-img"></div>
                  <p class="title">金融</p>
                </div>
                <div class="hover">
                  <div class="icon-img"></div>
                  <p class="title">金融</p>
                </div>
              </a>
            </div>
            <div class="icon-list">
              <a href="/serve-list?id=5">
                <div class="default">
                  <div class="icon-img"></div>
                  <p class="title">农民工</p>
                </div>
                <div class="hover">
                  <div class="icon-img"></div>
                  <p class="title">农民工</p>
                </div>
              </a>
            </div>
            <div class="icon-list">
              <a href="/serve-list?id=6">
                <div class="default">
                  <div class="icon-img"></div>
                  <p class="title">社会组织</p>
                </div>
                <div class="hover">
                  <div class="icon-img"></div>
                  <p class="title">社会组织</p>
                </div>
              </a>
            </div>
          </div>
        </div>
      </div>
    </section>

    <section class="serve-btn-box row">
      <div class="list-box col">
        <div class="box clearfix">
          <a href="http://www.scncpzs.com/" target="_blank">
            <span class="list-icon"></span>
            <span class="list-name">产品质量安全追溯</span>
          </a>
        </div>
      </div>
      <div class="list-box col">
        <div class="box clearfix">
          <a href="http://202.61.89.190:8080/" target="_blank">
            <span class="list-icon"></span>
            <span class="list-name">农产品质量安全监测</span>
          </a>
        </div>
      </div>
      <div class="list-box col">
        <div class="box clearfix">
          <a href="javascript:void(0)">
            <span class="list-icon"></span>
            <span class="list-name">农情快报</span>
          </a>
        </div>
      </div>
      <div class="list-box col">
        <div class="box clearfix">
          <a href="javascript:void(0)">
            <span class="list-icon"></span>
            <span class="list-name">农技问答</span>
          </a>
        </div>
      </div>
      <div class="list-box col">
        <div class="box clearfix">
          <a href="javascript:void(0)">
            <span class="list-icon"></span>
            <span class="list-name">农药数字监督</span>
          </a>
        </div>
      </div>
      <div class="list-box col">
        <div class="box clearfix">
          <a href="http://xue.yszn.net.cn/" target="_blank">
            <span class="list-icon"></span>
            <span class="list-name">双创培训</span>
          </a>
        </div>
      </div>
      <div class="list-box col">
        <div class="box clearfix">
          <a href="http://www.moa.gov.cn/sj/index.htm" target="_blank">
            <span class="list-icon"></span>
            <span class="list-name">农事日历</span>
          </a>
        </div>
      </div>
    </section>

    <section class="news-box">
      <div class="list-box" v-for="(item,index) in serve">
        <div class="container">
          <a href="">
            <div class="row">
              <div class="col-lg-1 news-date">
                <p class="news-day">{{item.create_time | formatDateD}}</p>
                <p class="news-year">{{item.create_time | formatDateYM}}</p>
              </div>
              <div :class="{'col-lg-9':item.thumbnail,'col-lg-11':!item.thumbnail}">
                <p class="name">
                  {{item.title}}
                </p>
                <p class="intro">
                  {{item.excerpt}}
                </p>
              </div>
              <div class="col-lg-2" v-if="item.thumbnail">
                <div class="img-box">
                  <img :src="$config.apiUrl + item.thumbnail" alt="">
                </div>
              </div>
            </div>
          </a>
        </div>
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
                show: 5,
                listType:"ARTICLE-TYPE-5DDA3FF9C6531",
                serve: [],
                dataList: [],
                hotList: [],
                recommendList: []
            }
        },
        mounted() {
            this.getServe();
            this.getRecommend();
            this.getHot();
        },
        methods: {
            // 获取服务信息
            getServe() {
                // axios.get("../../static/data/serve.json", {}).then(res => {
                //     this.serve = res.data
                //     this.$nextTick(() => {
                //         this.initSwiper()
                //     })
                // })
                let apiUrl = this.$config.apiUrl + 'portal/article/data/read';
                axios.get(apiUrl, {
                    params: {
                        type: this.listType,
                        page: 1,
                        limit: 8
                    }
                }).then((res) => {
                    if (res.data.code === 200) {
                        this.serve = res.data.data.data;
                        // console.log(this.serve)
                    }
                })
            },
            //获取推荐
            getRecommend() {
                let apiUrl = this.$config.apiUrl + 'portal/article/data/read';
                axios.get(apiUrl, {
                    params: {
                        type: this.listType,
                        page: 1,
                        limit: 3,
                        recommended: 1
                    }
                }).then((res) => {
                    if (res.data.code === 200) {
                        this.recommendList = res.data.data.data;
                        this.$nextTick(() => {
                            this.initSwiper()
                        })
                    }
                })
            },
            // 获取热门
            getHot() {
                let apiUrl = this.$config.apiUrl + 'portal/article/data/read';
                axios.get(apiUrl, {
                    params: {
                        type: this.listType,
                        page: 1,
                        limit: 6,
                        hot: 1
                    }
                }).then((res) => {
                    if (res.data.code === 200) {
                        this.hotList = res.data.data.data;
                    }
                })
            },
            // 初始化swiper
            initSwiper() {
                new Swiper('.serve-swiper', {
                    autoplay: {
                        delay: 3000,
                        stopOnLastSlide: false,
                        disableOnInteraction: true,
                    },
                    observer: true,
                    // 如果需要分页器
                    pagination: {
                        el: '.serve-pagination',
                    },
                })
            }
        },
        filters: {
            formatDateYM: function (value) {
                let date = new Date(value);
                let y = date.getFullYear();
                let MM = date.getMonth() + 1;
                MM = MM < 10 ? ('0' + MM) : MM;
                return y + '-' + MM;
            },
            formatDateMD: function (value) {
                let date = new Date(value);
                let MM = date.getMonth() + 1;
                MM = MM < 10 ? ('0' + MM) : MM;
                let d = date.getDate();
                d = d < 10 ? ('0' + d) : d;
                return MM + '-' + d;
            },
            formatDateD: function (value) {
                let date = new Date(value);
                let d = date.getDate();
                d = d < 10 ? ('0' + d) : d;
                return d;
            }
        }
    }
</script>
<style src="../assets/css/serve.css" scoped></style>
<style src="../assets/css/news.css" scoped></style>
<style>

  #serve .broadside-box {
    display: none;
  }

  .serve-pagination .swiper-pagination-bullet {
    opacity: 1;
    background-color: #fff;
    width: 10px;
    height: 10px;
  }

  .serve-pagination .swiper-pagination-bullet-active {
    background-color: #37cf9f;
    width: 38px;
    border-radius: 5px;

  }
</style>
