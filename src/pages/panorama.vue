<template>
  <div class="panorama" :class="{'inside-box':show !== 1}">
    <publicHead :show="show" :rightBtn="true"/>
    <section class="public-banner">
      <img class="banner-img" src="../../static/images/panoramaBanner.jpg" alt="">
    </section>
    <div class="village-box">
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
          <div class="hot-list" :class="{'col-lg-6':index<2,'col-lg-3 col-sm-6':index>=2,'panorama':index<2}"
               v-for="(item,index) in hot">
            <div class="box">
              <a :href="item.tour" :target="item.blank">
                <div class="img-box">
                  <img :src="$config.apiUrl + item.tour_img" alt="" :title="item.name">
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
                      @input="getHotVillage"
        ></b-pagination>
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
                show: 1,
                mobile: false,
                currentPage: 1,
                rows: '',
                townId: "510703000000",
                perPage: 10,
                cityData: [{"label": "全部", "value": 0}],
                classify: [],
                villageCId: this.$route.query.cid,
                classifyOption: [],
                hotOrGood: 'hot',
                hot: [],
                nowTown: '0'
            }
        },
        mounted: function () {
            this.mobile = window.innerWidth < 992;
            window.onresize = res => {
                this.mobile = window.innerWidth < 992;
            };
            this.getCityData();
            // this.getHotVillage();
            this.getVillageClassify();
        },
        methods: {
            // 点赞
            setLike: function (data, uniqid) {
                for (let i = 0; i < data.length; i++) {
                    if (uniqid === data[i].uniqid) {
                        if (!data[i].isLike) {
                            axios.put(this.$config.apiUrl + "village/data/like", {
                                uniqid: uniqid
                            }).then(res => {
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
                let cid = this.villageCId > -1 ? this.villageCId : -1;
                let apiUrl = this.$config.apiUrl + 'village/type/read';
                axios.get(apiUrl).then((res) => {
                    if (res.data.code === 200) {
                        for (let i = 0; i < res.data.data.length; i++) {
                            if (parseInt(cid) === i) {
                                res.data.data[i].checked = true
                                this.classifyOption.push(res.data.data[i].uniqid);
                            } else {
                                res.data.data[i].checked = false
                            }
                        }
                        this.classify = res.data.data;
                        this.getHotVillage();
                    }
                });

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
            setClassify: function (index) {
                this.currentPage = 1;
                this.classifyOption = [];
                this.classify[index].checked = !this.classify[index].checked;
                this.classify.forEach((value, index) => {
                    if (value.checked) {
                        this.classifyOption.push(value.uniqid);
                    }
                });
                this.getHotVillage();
            },
            // 获取村落数据
            getHotVillage: function () {
                let apiUrl = this.$config.apiUrl + 'village/data/read';
                let perPage = this.perPage;
                axios.get(apiUrl, {
                    params: {
                        page: this.currentPage,
                        limit: perPage,
                        town: this.nowTown,
                        type: this.classifyOption
                    }
                }).then((res) => {
                    if (res.data.code === 200) {
                        let resData = res.data.data;
                        this.rows = resData.total;

                        resData.data.forEach((v,i)=>{
                            if(v.tour ===''){
                                v.tour = "javascript:void(0)";
                                v.blank = "";
                            }else{
                                v.blank = "_blank";
                            }
                        });
                        this.hot = resData.data;
                    }
                })
            },

            //更新数据
            changeData() {
                this.currentPage = 1;
                this.getHotVillage()
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
