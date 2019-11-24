<template>
  <div class="village" :class="{'inside-box':show != 1}">
    <publicHead :show="show" :rightBtn="true"/>
    <div class="village-box">
      <p class="village-banner">
        <img class="img-fluid" src="../../static/images/villageBanner.jpg" alt="">
      </p>

      <div class="village-classify">
        <span class="hot" @click="setHotOrGoodSort('hot'),sortByKey(hot,'hits')" :class="{'active':hotOrGood == 'hot'}">热门</span>
        <span class="good" @click="setHotOrGoodSort('good'),sortByKey(hot,'like')"
              :class="{'active':hotOrGood == 'good'}">好评</span>
        <span v-if="!mobile" class="separator"></span>
        <br v-if="mobile">
        <span class="name">乡镇</span>
        <div class="city-select-box">
          <select v-model="nowTown" class="citySelect custom-select" @change="changeData">
            <option :value="list.value" v-for="list in cityData">{{list.label}}</option>
          </select>
        </div>
        <span v-if="!mobile" class="separator"></span>
        <br v-if="mobile">
        <span class="name">特色</span>
        <span class="village-classify-list" :class="{'active':list.checked}" v-for="(list,index) in classify"
              @click="setClassify(index,list.uniqid)">{{list.name}}</span>
      </div>
    </div>
    <div class="common-hot-box">
      <div class="container">
        <div class="row">
          <div class="hot-list col-lg-3 col-sm-6" v-for="(item,index) in hot">
            <div class="box">
              <a :href="'village-home?vid='+item.uniqid">
                <div class="img-box">
                  <img :src="$config.apiUrl + item.thumbnail" alt="" :title="item.name">
                </div>
              </a>

              <div class="text-box">
                <p class="clearfix">
                  <a :href="'village-home?vid='+item.uniqid">
                    <span class="name">{{item.name}}</span>
                  </a>
                  <span class="address">{{item.town_text}}</span>
                </p>
                <p class="type-box">
                  <span class="type" v-for="list in item.type">
                    {{list}}
                  </span>
                </p>
                <p class="clearfix">
                  <span class="look">{{item.hits}}</span>
                  <!--                  <span class="comment">{{item.comment}}</span>-->
                  <span class="like" :class="{'likeActive':hot[index].isLike===true}"
                        @click="setLike(hot,item.uniqid)">{{item.like}}</span>
                </p>
              </div>
            </div>
          </div>
        </div>

        <b-pagination v-if="rows/perPage>1" hide-goto-end-buttons use-router align="center"
                      v-model="currentPage"
                      :total-rows="rows"
                      :per-page="perPage"
                      @input="paging"
        ></b-pagination>
      </div>
    </div>

    <div class="hot-recommend-box">
      <div class="container">
        <p class="hot-recommend-title">热门推荐</p>
        <div class="row">
          <div class="col-lg-3 col-sm-6 list-item" v-for="item in hotRecommend">
            <div class="box">
              <a href="">
                <div class="img-box">
                  <img :src="$config.apiUrl + item.thumbnail" alt="" :title="item.title">
                </div>
                <p class="name" :title="item.title">{{item.title}}</p>
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
                mobile: false,
                currentPage: 1,
                rows: '',
                townId:"510703000000",
                perPage: 12,
                cityData: [{"label": "全部", "value": 0}],
                classify: [],
                hotOrGood: 'hot',
                hot: [],
                nowTown: '0',
                hotRecommend: []
            }
        },
        mounted: function () {
            this.mobile = window.innerWidth < 992;
            window.onresize = res => {
                this.mobile = window.innerWidth < 992;
            };
            this.getCityData();
            this.getHotVillage();
            this.getHotRecommend();
            this.getVillageClassify();
        },
        methods: {
            // 点赞
            setLike: function (data, uniqid) {
                console.log(uniqid)
                for (let i = 0; i < data.length; i++) {
                    if (uniqid === data[i].uniqid) {
                        if (!data[i].isLike) {
                            axios.put(this.$config.apiUrl + "village/data/like", {
                                uniqid: uniqid
                            }).then(res => {
                                console.log(res)
                                if (res.data.code === 200) {
                                    data[i].like = parseInt(data[i].like) + 1;
                                    data[i].isLike = true
                                }
                            })
                        }
                    }
                }
            },
            // 获取乡镇信息
            getCityData: function () {
                // let self = this
                // axios.get('../../static/data/city.json').then(function (res) {
                //     self.cityData = res.data
                // })
                let apiUrl = this.$config.apiUrl + 'region/read';
                let town = this.townId;
                axios.get(apiUrl).then((res) => {
                    if (res.data.code === 200) {
                        res.data.data.forEach((value, index) => {
                            if (value.parent == town) {
                                this.cityData.push(value)
                            }
                        });
                    }
                })
            },
            // 获取村落分类
            getVillageClassify() {
                let apiUrl = this.$config.apiUrl + 'village/type/read';
                axios.get(apiUrl).then((res) => {
                    if (res.data.code === 200) {
                        for (let i = 0; i < res.data.data.length; i++) {
                            res.data.data[i].checked = false
                        }
                        this.classify = res.data.data;
                    }
                })
            },
            //热门和好评排序
            setHotOrGoodSort: function (num) {
                if (num === 'hot') {
                    this.hotOrGood = 'hot'
                } else {
                    this.hotOrGood = 'good'
                }
            },
            // 选择特色分类
            setClassify: function (index, uniqid) {
                console.log(uniqid)
                this.classify[index].checked = !this.classify[index].checked;
            },
            // 获取村落数据
            getHotVillage: function () {
                // let self = this
                // axios.get('../../static/data/tuijiancunzhuang.json').then(function (res) {
                //     self.hot = res.data;
                //     self.sortByKey(self.hot, 'look');
                // })
                let apiUrl = this.$config.apiUrl + 'village/data/read';
                let perPage = this.perPage;
                axios.get(apiUrl, {
                    params: {
                        page: 1,
                        limit: perPage
                    }
                }).then((res) => {
                    if (res.data.code === 200) {
                        let resData = res.data.data;
                        this.rows = resData.total;
                        this.hot = resData.data;
                    }
                })
            },
            // 翻页
            paging() {
                let apiUrl = this.$config.apiUrl + 'village/data/read';
                let perPage = this.perPage;
                axios.get(apiUrl, {
                    params: {
                        page: this.currentPage,
                        limit: perPage
                    }
                }).then((res) => {
                    if (res.data.code === 200) {
                        let resData = res.data.data;
                        this.rows = resData.total;
                        this.hot = resData.data;
                    }
                })
            },

            //更新数据
            changeData() {
                console.log(this.nowTown)
            },
            // 获取热门推荐内容
            getHotRecommend: function () {
                // let self = this
                // axios.get('../../static/data/hotRecommend.json').then(function (res) {
                //     self.hotRecommend = res.data;
                // })

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
