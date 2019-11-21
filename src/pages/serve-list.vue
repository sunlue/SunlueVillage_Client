<template>
  <div id="serve-list" class="inside-box">
    <publicHead :show="show" :rightBtn="false"/>
    <section class="serve-list-box">
      <b-nav tabs align="center">
        <b-nav-item :active="tabId==1" @click="getServeData(1)">&emsp;党宣 &emsp;</b-nav-item>
        <b-nav-item :active="tabId==2" @click="getServeData(2)">&emsp;生产&emsp;</b-nav-item>
        <b-nav-item :active="tabId==3" @click="getServeData(3)">&emsp;科技&emsp;</b-nav-item>
        <b-nav-item :active="tabId==4" @click="getServeData(4)">&emsp;金融&emsp;</b-nav-item>
        <b-nav-item :active="tabId==5" @click="getServeData(5)"> &nbsp;农民工&nbsp;</b-nav-item>
        <b-nav-item :active="tabId==6" @click="getServeData(6)">社会组织</b-nav-item>
      </b-nav>
    </section>
    <section class="news-box serve-list-news">
      <div class="box">
        <div class="list-box" v-for="(item,index) in serveData">
          <div class="container">
            <router-link class="search-btn" :to="{ path: 'article', query: { id: item.id }}">
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
            </router-link>
          </div>
        </div>
      </div>

      <b-pagination-nav v-if="totalPage>1" hide-goto-end-buttons :link-gen="$myfunction.linkGen" :number-of-pages="totalPage" use-router align="center"></b-pagination-nav>

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
                show: 4,
                totalPage:10,
                tabId: this.$route.query.id ? this.$route.query.id : 1,
                serveData: []
            }
        },
        mounted() {
            this.initServeData();
        },
        methods: {
            //初始当前服务数据
            initServeData: function () {
                axios.get("../../static/data/newsAll.json", {
                    id: this.tabId
                }).then(res => {
                    this.serveData = res.data
                })
            },
            // 获取指定服务类型数据
            getServeData: function (id) {
                this.tabId = id
                axios.get("../../static/data/newsAll.json", {
                    id: this.tabId
                }).then(res => {
                    this.serveData = res.data
                })
            },
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
<style src="../assets/css/serve.css" scoped></style>
<style src="../assets/css/news.css" scoped></style>
<style>
  #serve-list .broadside-box {
    display: none;
  }

  .serve-list-box .nav {
    border: none;
  }

  .serve-list-box .nav .nav-item .nav-link {
    border: none;
    color: #999;
    font-size: 24px;
    font-weight: 300;
  }

  .serve-list-box .nav .nav-item .nav-link:hover {
    color: #37cf9f;
    background-color: #fff;
  }

  .serve-list-box .nav .nav-item .nav-link.active {
    color: #37cf9f;
  }
</style>
