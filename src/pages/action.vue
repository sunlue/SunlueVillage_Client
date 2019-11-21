<template>
  <div id="action">
    <publicHead :show="show" :rightBtn="true"/>
    <section class="public-banner">
      <img class="banner-img" src="../../static/images/actionBanner.jpg" alt="">
    </section>
    <section class="content-box">
      <div class="container">
        <div class="row">
          <div class="col-lg-4 col-sm-6 list-box" v-for="(item,index) in actionArr">
            <router-link :to="{ path: 'article', query: { id: item.id }}">
              <div class="box">
                <div class="img-box">
                  <img :src="item.imgUrl" alt="">
                </div>
                <div class="text-box clearfix">
                  <div class="date-box">
                    <p class="date-day">{{item.date | formatDateD}}</p>
                    <p class="date-year">{{item.date | formatDateYM}}</p>
                  </div>
                  <div class="name-box">
                    {{item.name}}
                  </div>
                </div>
              </div>
            </router-link>
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
        components: {
            publicHead,
            publicFooter
        },
        data() {
            return {
                show: 31,//当前栏目
                actionArr: []
            }
        },
        mounted() {
            this.getActionData()
        },
        methods: {
            //获取特色活动数据
            getActionData: function () {
                axios.get("../../static/data/actionAll.json", {}).then(res => {
                    this.actionArr = res.data
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
<style src="../assets/css/action.css" scoped>
</style>
