<template>
  <div class="village" :class="{'inside-box':show != 1}">
    <publicHead :show="show" :rightBtn="true"/>
    <div class="village-box">
      <p class="village-banner">
        <img class="img-fluid" src="../../static/images/villageBanner.jpg" alt="">
      </p>

      <div class="village-classify">
        <span class="hot" @click="setHotOrGoodSort('hot'),sortByKey(hot,'look')" :class="{'active':hotOrGood == 'hot'}">热门</span>
        <span class="good" @click="setHotOrGoodSort('good'),sortByKey(hot,'like')"
              :class="{'active':hotOrGood == 'good'}">好评</span>
        <span class="separator"></span>
        <span class="name">乡镇</span>
        <div class="city-select-box">
          <select class="citySelect custom-select" name="" id="">
            <option :value="list.id" v-for="list in cityData">{{list.name}}</option>
          </select>
        </div>
        <span class="separator"></span>
        <span class="name">特色</span>
        <span class="village-classify-list" :class="{'active':list.checked}" v-for="(list,index) in classify"
              @click="setClassify(index)">{{list.name}}</span>
      </div>
    </div>
    <div class="common-hot-box">
      <div class="container">
        <div class="row">
          <div class="hot-list col-lg-3" v-for="(item,index) in hot">
            <div class="box">
              <div class="img-box">
                <router-link class="search-btn"
                             :to="{ path: 'village-home', query: { vid: item.id }}">
                  <img v-bind:src="item.imgUrl" alt="" :title="item.name">
                </router-link>
              </div>
              <div class="text-box">
                <p class="clearfix">
                  <router-link class="search-btn"
                               :to="{ path: 'village-home', query: { vid: item.id }}">
                    <span class="name">{{item.name}}</span>
                  </router-link>
                  <span class="address">{{item.address}}</span>
                </p>
                <p class="type-box">
                  <span class="type" v-for="list in item.villageType">
                    {{list}}
                  </span>
                </p>
                <p class="clearfix">
                  <span class="look">{{item.look}}</span>
                  <span class="comment">{{item.comment}}</span>
                  <span class="like" :class="{'likeActive':hot[index].isLike===true}"
                        @click="$myfunction.setLike(hot,item.id,)">{{item.like}}</span>
                </p>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>

    <div class="hot-recommend-box">
      <div class="container">
        <p class="hot-recommend-title">热门推荐</p>
        <div class="row">
          <div class="col-lg-3 list-item" v-for="item in hotRecommend">
            <div class="box">
              <a href="">
                <div class="img-box">
                  <img :src="item.imgUrl" alt="" :title="item.name">
                </div>
                <p class="name" :title="item.name">{{item.name}}</p>
              </a>
            </div>
          </div>
        </div>
      </div>
    </div>

    <publicFooter/>

  </div>
</template>
<script>
    import publicHead from "../components/publicHead"
    import publicFooter from "../components/publicFooter"
    import axios from "axios"

    export default {
        name: "village",
        components: {
            publicHead,
            publicFooter
        },
        data() {
            return {
                show: 2,
                cityData: [],
                classify: [
                    {name: '土货', checked: false},
                    {name: '农场', checked: false},
                    {name: '康养', checked: false},
                    {name: '景点', checked: false},
                    {name: '民俗', checked: false},
                    {name: '民宿', checked: false}
                ],
                hotOrGood: 'hot',
                hot: [],
                hotRecommend: []
            }
        },
        mounted: function () {
            this.getCityData();
            this.getHotVillage();
            this.getHotRecommend();
        },
        methods: {
            // 获取乡镇信息
            getCityData: function () {
                let self = this
                axios.get('../../static/data/city.json').then(function (res) {
                    self.cityData = res.data
                })
            },
            //设置热门和好评分类
            setHotOrGoodSort: function (num) {
                if (num === 'hot') {
                    this.hotOrGood = 'hot'
                } else {
                    this.hotOrGood = 'good'
                }
            },
            // 选择特色分类
            setClassify: function (index) {
                this.classify[index].checked = !this.classify[index].checked;
            },
            // 获取热门村庄
            getHotVillage: function () {
                let self = this
                axios.get('../../static/data/tuijiancunzhuang.json').then(function (res) {
                    self.hot = res.data;
                    self.sortByKey(self.hot, 'look');
                })
            },
            // 获取热门推荐内容
            getHotRecommend: function () {
                let self = this
                axios.get('../../static/data/hotRecommend.json').then(function (res) {
                    self.hotRecommend = res.data;
                })
            },
            //数组对象排序
            sortByKey: function (array, key) {
                return array.sort(function (a, b) {
                    let x = parseInt(a[key]);
                    let y = parseInt(b[key]);
                    return ((x > y) ? -1 : ((x < y) ? 1 : 0));
                })
            }
        }
    }
</script>

<style src="../assets/css/village.css" scoped>
</style>
