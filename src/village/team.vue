<template>
  <div id="village-culture" class="inside-box">
    <villageHead :show="show" :vid="villageId"/>
    <section>
      <div class="village-banner">
        <img class="img-fluid" src="../../static/images/teamBanner.jpg" alt="">
      </div>
      <div class="content-box">
        <div class="container-fluid">

          <div class="history list-jump">
            <p class="title">党支部</p>
            <div class="swiper-container branch-swiper">
              <div class="swiper-wrapper">
                <div class="swiper-slide" v-for="item in villageArr.specialty">
                  <a href="">
                    <div class="img-box">
                      <img :src="item.img" alt="">
                    </div>
                    <p class="name" :title="item.name">
                      {{item.name}}
                    </p>
                  </a>
                </div>
              </div>
              <!-- 如果需要分页器 -->
              <div class="swiper-pagination branch-pagination"></div>

              <!-- 如果需要导航按钮 -->
              <div class="swiper-button-prev branch-prev"></div>
              <div class="swiper-button-next branch-next"></div>
            </div>
          </div>

          <div class="history list-jump">
            <p class="title">两新组织</p>
            <div class="swiper-container team-swiper">
              <div class="swiper-wrapper">
                <div class="swiper-slide" v-for="item in villageArr.specialty">
                  <a href="">
                    <div class="img-box">
                      <img :src="item.img" alt="">
                    </div>
                    <p class="name" :title="item.name">
                      {{item.name}}
                    </p>
                  </a>
                </div>
              </div>
              <!-- 如果需要分页器 -->
              <div class="swiper-pagination team-pagination"></div>

              <!-- 如果需要导航按钮 -->
              <div class="swiper-button-prev team-prev"></div>
              <div class="swiper-button-next team-next"></div>
            </div>
          </div>
        </div>
      </div>
    </section>

    <div class="right-anchor">
      <p class="list" :class="{'active':showItem==0}" @click="goLocation(0)">党支部</p>
      <p class="list" :class="{'active':showItem==1}" @click="goLocation(1)">两新组织</p>
      <p class="list" @click="returnTop">返回顶部</p>
    </div>
    <publicFooter/>
  </div>
</template>
<script>
    import villageHead from "../components/villageHead"
    import publicFooter from "../components/publicFooter"
    import Swiper from "swiper"
    import axios from "axios"
    import "swiper/css/swiper.min.css"

    export default {
        components: {
            villageHead,
            publicFooter
        },
        mounted() {
            this.getVillageData();
            window.addEventListener('scroll', this.setAnchor);
        },
        destroyed() { //页面离开后销毁，防止切换路由后上一个页面监听scroll滚动事件会在新页面报错问题
            window.removeEventListener('scroll', this.setAnchor)
        },
        data() {
            return {
                show: 4,
                villageId: this.$route.query.vid,
                villageArr: [],
                showItem: 0
            }
        },
        methods: {
            //获取内容
            getVillageData() {
                axios.get("../../static/data/villageResource.json", {
                    id: this.villageId
                }).then(res => {
                    this.villageArr = res.data;

                    this.$nextTick(() => {
                        this.initSwiper();
                    })

                })
            },
            //右侧锚点
            setAnchor() {
                let windowTop = document.documentElement.scrollTop;
                let el = document.querySelectorAll(".list-jump");
                el.forEach((item, index) => {
                    let elTop = item.offsetTop;
                    let height = item.clientHeight;
                    if (windowTop > elTop - height) {
                        this.showItem = index
                    }
                })
            },
            // 回到顶部
            returnTop() {
                let ani = setInterval(function () {
                    let windowTop = document.documentElement.scrollTop;
                    if (windowTop > 0) {
                        document.documentElement.scrollTop -= 40;
                    } else {
                        clearInterval(ani);
                    }
                }, 10);
            },
            //去指定位置
            goLocation(i) {
                document.documentElement.scrollTop = document.querySelectorAll(".list-jump")[i].offsetTop;
            },
            // 初始化swiper
            initSwiper() {
                new Swiper('.branch-swiper', {
                    observer: true,
                    slidesPerView: 3,
                    slidesPerColumn: 2,
                    spaceBetween: 30,
                    // 如果需要分页器
                    pagination: {
                        el: '.branch-pagination',
                    },
                    // 如果需要前进后退按钮
                    navigation: {
                        nextEl: '.branch-next',
                        prevEl: '.branch-prev',
                    },
                });

                new Swiper('.team-swiper', {
                    observer: true,
                    slidesPerView: 3,
                    slidesPerColumn: 2,
                    spaceBetween: 30,
                    // 如果需要分页器
                    pagination: {
                        el: '.team-pagination',
                    },
                    // 如果需要前进后退按钮
                    navigation: {
                        nextEl: '.team-next',
                        prevEl: '.team-prev',
                    },
                });
            }
        }
    }
</script>

<style src="../assets/css/villagePage.css" scoped></style>
<style>
  #village-culture .content-box img {
    max-width: 100%;
  }

  .swiper-container .swiper-pagination-bullet-active {
    background-color: #37cf9f;
  }

</style>
