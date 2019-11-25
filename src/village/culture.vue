<template>
  <div id="village-culture" class="inside-box">
    <villageHead :show="show" :vid="villageId"/>
    <section>
      <div class="village-banner">
        <img class="img-fluid" src="../../static/images/villageCultureBanner.jpg" alt="">
      </div>
      <div class="content-box">
        <div class="container-fluid">
          <div class="intro list-jump">
            <p class="title">村落概况</p>
            <div class="content">
              <div v-for="item in intro">
                <b-table class="table-bordered table-secondary" detailsShowing striped hover :items="item"></b-table>
              </div>
            </div>
          </div>
          <div class="culture list-jump">
            <p class="title">文物古迹</p>
            <div class="culture-list" v-for="(item,index) in culturalArr">
              <p class="name"><span class="num-btn">{{index + 1}}</span>{{item.name}}</p>
              <div class="details">{{item.excerpt}}</div>
            </div>
          </div>
          <div class="history list-jump">
            <p class="title">自然环境</p>
            <div class="history-content">
              <p>年平均温度：{{naturalArr.annual_mean_temperature}}℃</p>
              <p>年均降雨量：{{naturalArr.annual_rainfall}}ml</p>
              <p>年无霜期：{{naturalArr.annual_frost_free_period}}</p>
              <p>植被覆盖率：{{naturalArr.vegetation_coverage}}%</p>
              <p>气候特征：{{naturalArr.climate_characteristics}}</p>
              <p>主要耕地：{{naturalArr.main_land}}</p>
            </div>
          </div>
        </div>
      </div>
    </section>

    <div class="right-anchor">
      <p class="list" :class="{'active':showItem==0}" @click="goLocation(0)">村落概况</p>
      <p class="list" :class="{'active':showItem==1}" @click="goLocation(1)">文物古迹</p>
      <p class="list" :class="{'active':showItem==2}" @click="goLocation(2)">自然环境</p>
      <p class="list" @click="returnTop">返回顶部</p>
    </div>
    <publicFooter/>
  </div>
</template>
<script>
    import villageHead from "../components/villageHead"
    import publicFooter from "../components/publicFooter"
    import axios from "axios"

    export default {
        components: {
            villageHead,
            publicFooter
        },
        mounted() {
            this.getVillageData();
            this.getNatural();
            this.getCultural();
            window.addEventListener('scroll', this.setAnchor);
        },
        destroyed() { //页面离开后销毁，防止切换路由后上一个页面监听scroll滚动事件会在新页面报错问题
            window.removeEventListener('scroll', this.setAnchor)
        },
        data() {
            return {
                show: 2,
                villageId: this.$route.query.vid,
                villageArr: [],
                showItem: 0,
                intro: {},
                naturalArr: [],
                culturalArr:[]
            }
        },
        methods: {
            //获取内容
            getVillageData() {
                let apiUrl = this.$config.apiUrl + 'village/data/details';
                axios.get(apiUrl, {
                    params: {
                        uniqid: this.villageId
                    }
                }).then((res) => {
                    if (res.data.code === 200) {
                        let resData = res.data.data;
                        this.intro = [[{
                            "村落名称": resData.name,
                            "村落属性": resData.attribute === 1 ? "行政村" : "自然村",
                        }], [{
                            "户籍人口": resData.registered_population,
                            "户籍人口(男)": resData.registered_population_man,
                            "户籍人口(女)": resData.registered_population_woman,
                            "常住人口": resData.permanent_population,
                            "常住人口(男)": resData.permanent_population_man,
                            "常住人口(女)": resData.permanent_population_woman,
                        }], [{
                            "形成年代": resData.decade,
                            "村集体年收入": resData.collective_income,
                            "村人均年收入": resData.person_income,
                            "村域面积": resData.domain_area + "平方公里",
                            "村庄面积": resData.village_area + "平方公里",
                            "主要产业": resData.industry,
                        }]];
                    }
                })
            },
            // 获取自然资源
            getNatural() {
                let apiUrl = this.$config.apiUrl + 'village/natural/details';
                axios.get(apiUrl, {
                    params: {
                        village_id: this.villageId
                    }
                }).then((res) => {
                    this.naturalArr = res.data.data
                })
            },
            // 获取文物古迹
            getCultural() {
                let apiUrl = this.$config.apiUrl + 'village/relics/read';
                axios.get(apiUrl, {
                    params: {
                        village_id: this.villageId,
                        page:1,
                        limit:12
                    }
                }).then((res) => {
                    this.culturalArr = res.data.data.data
                })
            },
            //右侧锚点
            setAnchor() {
                let windowTop = document.documentElement.scrollTop;
                let el = document.querySelectorAll(".list-jump");
                el.forEach((item, index) => {
                    let elTop = item.offsetTop;
                    let height = item.clientHeight
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
            }
        }
    }
</script>

<style src="../assets/css/villagePage.css" scoped></style>
<style>
  #village-culture .content-box img {
    max-width: 100%;
  }
</style>
