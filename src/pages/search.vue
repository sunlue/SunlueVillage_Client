<template>
  <div id="news">
    <publicHead :show="show" :rightBtn="true"/>
    <section class="public-banner">
      <img class="banner-img" src="../../static/images/searchBanner.jpg" alt="">
      <div class="search-box">
        <span class="name">搜索</span>
        <div class="input-box">
          <input class="search-input" type="text" placeholder="请输入关键词" v-model="searchValue" v-on:keyup.enter="getNewsData">
          <span class="search-btn" @click="getNewsData"></span>
        </div>
      </div>
    </section>
    <section class="news-box">
      <div class="list-box" v-for="(item,index) in search">
        <div class="container">
          <a href="">
            <div class="row">
              <div class="offset-lg-2" :class="{'col-lg-9':item.imgUrl,'col-lg-11':!item.imgUrl}">
                <p class="name" v-html="ruleTitle(item.name)">
                </p>
                <p class="intro">
                  {{item.intro}}
                </p>
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
                show: 1,//当前栏目
                search: [],
                searchValue: decodeURIComponent(this.$route.query.antistop),
            }
        },
        mounted() {
            this.getNewsData()
        },
        methods: {
            //获取特色活动数据
            getNewsData: function () {
                console.log(1)
                axios.get("../../static/data/search.json", {}).then(res => {
                    this.search = res.data
                })
            },
            // 搜索词高亮
            ruleTitle(name) {
                let titleString = name;
                if (!titleString) {
                    return '';
                }
                if (this.searchValue && this.searchValue.length > 0) {
                    // 匹配关键字正则
                    let replaceReg = new RegExp(this.searchValue, 'g');
                    // 高亮替换v-html值
                    let replaceString = '<span class="search-text">' + this.searchValue + '</span>';
                    // 开始替换
                    titleString = titleString.replace(replaceReg, replaceString);
                }
                return titleString;
            }
        }
    }
</script>
<style src="../assets/css/search.css" scoped>
</style>
<style>
  .search-text {
    color: #37cf9f;
  }

  .list-box:hover .search-text {
    color: #fff;
  }
</style>
