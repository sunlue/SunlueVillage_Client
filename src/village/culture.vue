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
            <div class="content" v-html="villageArr.intro">
            </div>
          </div>
          <div class="culture list-jump">
            <p class="title">民俗文化</p>
            <div class="culture-list" v-for="(item,index) in villageArr.culture">
              <p class="name"><span class="num-btn">{{index + 1}}</span>{{item.name}}</p>
              <div class="details">{{item.content}}</div>
            </div>
          </div>
          <div class="history list-jump">
            <p class="title">历史沿革</p>
            <div class="history-content" v-html="villageArr.history">
            </div>
          </div>
        </div>
      </div>
    </section>

    <div class="right-anchor">
      <p class="list" :class="{'active':showItem==0}" @click="goLocation(0)">村落概况</p>
      <p class="list" :class="{'active':showItem==1}" @click="goLocation(1)">民俗文化</p>
      <p class="list" :class="{'active':showItem==2}" @click="goLocation(2)">历史沿革</p>
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
                showItem: 0
            }
        },
        methods: {
            //获取内容
            getVillageData() {
                axios.get("../../static/data/villageCulture.json", {
                    id: this.villageId
                }).then(res => {
                    this.villageArr = res.data
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
                let ani = setInterval(function(){
                    let windowTop = document.documentElement.scrollTop;
                    if(windowTop > 0){
                        document.documentElement.scrollTop -= 40;
                    }else{
                        clearInterval(ani);
                    }
                },10);
            },
            //去指定位置
            goLocation(i){
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
