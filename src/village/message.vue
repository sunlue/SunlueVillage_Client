<template>
  <div id="village-culture" class="inside-box">
    <villageHead :show="show" :vid="villageId"/>
    <section>
      <div class="village-banner">
        <img class="img-fluid" src="../../static/images/villageMessageBanner.jpg" alt="">
      </div>
      <div class="content-box">
        <div class="container-fluid">

          <div class="culture list-jump">
            <p class="title">信息动态</p>
            <a href="" class="row message-list" v-for="(item,index) in villageNewsArr">
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
            </a>
          </div>
        </div>
      </div>
    </section>
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
        },
        data() {
            return {
                show: 5,
                villageId: this.$route.query.vid,
                villageNewsArr: [],
                showItem: 0
            }
        },
        methods: {
            //获取内容
            getVillageData() {
                axios.get("../../static/data/newsAll.json", {
                    id: this.villageId
                }).then(res => {
                    this.villageNewsArr = res.data;
                    console.log(res.data)
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
<style src="../assets/css/villagePage.css" scoped></style>

