<template>
  <div id="article-page">
    <publicHead :show="show" :rightBtn="false"/>
    <section class="public-banner">
      <img class="banner-img" src="../../static/images/articleBanner.jpg" alt="">
    </section>
    <section class="article-box">
      <div class="box">
        <div class="container-fluid">
          <div class="row">

            <div class="col-lg-9">
              <p class="name">
                {{articleArr.title}}
              </p>
              <div class="clearfix">
                <span class="date">
                  {{articleArr.create_time | formatDate}}
                </span>
                <div class="bdsharebuttonbox">
                  <a href="#" class="share bds_weixin" data-cmd="weixin" title="分享到微信">分享</a>
                </div>
                <span class="hit">
                  {{articleArr.hits}}
                </span>
              </div>
              <div class="content-box" v-html="articleArr.content"></div>
              <p class="prev-next-box clearfix">
                <a class="float-left prev-btn" :href="'/article?id='+articleArr.prev.uniqid">
                  {{articleArr.prev.title}}
                </a>
                <a class="float-right next-btn" :href="'/article?id='+articleArr.next.uniqid">
                  {{articleArr.next.title}}
                </a>
              </p>

              <p class="retreat-btn" @click="back">
                返回列表
              </p>
            </div>
            <div class="col-lg-3">
              <a class="link-btn" href="/map">村落地图导览</a>
              <a class="link-btn" href="/panorama">VR 720°全景体验</a>
              <!--<router-link class="link-btn" :to="{ path: 'article', query: { id: articleId }}">精品语音讲解</router-link>-->

              <p class="recommend-title">热门推荐</p>
              <div class="img-box" v-for="item in hotRecommend">
                <a :href="'/article?id='+item.uniqid">
                  <img :src="$config.apiUrl + item.thumbnail" alt="" :title="item.title">
                  <p class="recommend-list-title" :title="item.title">{{item.title}}</p>
                </a>
              </div>
            </div>
          </div>
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
        data() {
            return {
                show: 99,
                articleId: this.$route.query.id,
                articleArr: {"prev":{},"next":{}},
                hotRecommend:[]
            }
        },
        components: {
            publicHead,
            publicFooter
        },
        mounted() {
            this.getArticle();
            this.getHotData();
            const self = this;
            setTimeout(() => {
                self.setShare()
            }, 0);
        },
        methods: {
            back(){
                this.$router.back();
            },
            getArticle() {
                let apiUrl = this.$config.apiUrl + 'portal/article/data/details';
                axios.get(apiUrl, {
                    params: {
                        uniqid: this.articleId,
                    }
                }).then((res) => {
                    if (res.data.code === 200) {
                        let resData = res.data.data;
                        this.articleArr = resData;
                    }
                })
            },
            //热门推荐
            getHotData(){
                let apiUrl = this.$config.apiUrl + 'portal/article/data/read';
                axios.get(apiUrl, {
                    params: {
                        page: 1,
                        limit: 4
                    }
                }).then((res) => {
                    if (res.data.code === 200) {
                        let resData = res.data.data;
                        this.hotRecommend = resData.data;
                    }
                })
            },
            setShare() {
                //分享相关代码
                window._bd_share_config = {
                    "common": {
                        "bdSnsKey": {},
                        "bdText": "",
                        "bdMini": "1",
                        "bdMiniList": false,
                        "bdPic": "",
                        "bdStyle": "1",
                        "bdSize": "24"
                    },
                    "share": {}
                };
                const s = document.createElement('script');
                s.type = 'text/javascript';
                s.src = 'http://bdimg.share.baidu.com/static/api/js/share.js?v=89860593.js?cdnversion=' + ~(-new Date() / 36e5);
                document.body.appendChild(s);
            }
        },
        filters: {
            formatDate: function (value) {
                let date = new Date(value);
                let y = date.getFullYear();
                let MM = date.getMonth() + 1;
                let d = date.getDate();
                MM = MM < 10 ? ('0' + MM) : MM;
                d = d < 10 ? ('0' + d) : d;
                return y + '-' + MM + '-' + d;
            }
        }
    }
</script>

<style src="../assets/css/article.css" scoped>
</style>
<style>
  #article-page .broadside-box {
    display: none;
  }

  #article-page .content-box img {
    max-width: 100%;
  }
</style>
