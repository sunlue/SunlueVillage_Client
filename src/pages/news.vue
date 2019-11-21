<template>
  <div id="news">
    <publicHead :show="show" :rightBtn="true"/>
    <section class="public-banner">
      <img class="banner-img" src="../../static/images/newsBanner.jpg" alt="">
    </section>
    <section class="news-box">
      <div class="list-box" v-for="(item,index) in newsArr">
        <div class="container">
          <a href="">
            <div class="row">
              <div class="col-lg-1 news-date">
                <p class="news-day">{{item.date | formatDateD}}</p>
                <p class="news-year">{{item.date | formatDateYM}}</p>
              </div>
              <div :class="{'col-lg-9':item.imgUrl,'col-lg-11':!item.imgUrl}">
                <p class="name">
                  {{item.name}}
                </p>
                <p class="intro">
                  {{item.intro}}
                </p>
              </div>
              <div class="col-lg-2" v-if="item.imgUrl">
                <div class="img-box">
                  <img :src="item.imgUrl" alt="">
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

    export default {
        components: {
            publicHead,
            publicFooter
        },
        data() {
            return {
                show: 31,//当前栏目
                newsArr: []
            }
        },
        mounted() {
            this.getNewsData()
        },
        methods: {
            //获取特色活动数据
            getNewsData: function () {
                axios.get("../../static/data/newsAll.json", {}).then(res => {
                    this.newsArr = res.data
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
            formatDateD: function (value) {
                let date = new Date(value);
                let d = date.getDate();
                d = d < 10 ? ('0' + d) : d;
                return d;
            }
        }
    }
</script>
<style src="../assets/css/news.css" scoped>
</style>
