<template>
  <div id="village-home" class="inside-box">
    <villageHead :show="show" :vid="villageId"/>
    <section class="village-box">
      <div class="swiper-container village-banner">
        <div class="swiper-wrapper">
          <div class="swiper-slide" v-for="item in villageArr.photo">
            <div class="img-box">
              <img :src="item" alt="">
            </div>
          </div>
        </div>
        <div class="swiper-button-prev village-banner-prev"></div>
        <div class="swiper-button-next village-banner-next"></div>
      </div>

      <div class="village-thumbs-box">
        <div class="swiper-container village-thumbs">
          <div class="swiper-wrapper">
            <div class="swiper-slide" v-for="item in villageArr.photo">
              <div class="img-box">
                <img :src="item" alt="">
              </div>
            </div>
          </div>
        </div>
      </div>

      <div class="content-box" :class="{'content-box-close':boxClose}">
        <div class="close-btn" @click="boxClose=!boxClose"></div>

        <div class="icon-box">
          <p class="icon-list">
            <a :href="villageArr.panorama" target="_blank">
              <i class="icon"></i>
              <span class="text">VR</span>
            </a>
          </p>
          <p class="icon-list" @click="showModal">
            <i class="icon"></i>
            <span class="text">视频</span>
          </p>
          <p class="icon-list" :class="{'audio-play':audioPlay}" @click="playAudio">
            <i class="icon"></i>
            <span class="text">解说</span>
            <audio class="village-audio" :src="villageArr.audio"></audio>
          </p>
        </div>
        <div class="details-box">
          <p>
            <span class="name">{{villageArr.name}}</span>
            <span class="address">{{villageArr.address}}</span>
          </p>
          <p>
            <span class="tag" v-for="item in villageArr.tag">{{item}}</span>
          </p>
          <p>
            <span class="classify" v-for="item in villageArr.classify">{{item}}</span>
          </p>
          <p>
            <span class="intro">{{villageArr.intro}}</span>
            <router-link class="more" :to="{ path: 'village-culture', query: { vid:villageId}}">
              更多
            </router-link>
          </p>
        </div>
      </div>

    </section>

    <b-modal id="video-modal" centered size="xl" :title="villageArr.name" hide-footer>
      <video class="village-home-video" :src="villageArr.video" controls></video>
    </b-modal>

    <publicFooter/>
  </div>
</template>
<script>
    import villageHead from "../components/villageHead"
    import publicFooter from "../components/publicFooter"
    import Swiper from "swiper"
    import axios from "axios"
    import "swiper/css/swiper.min.css"

    export default {
        components: {
            villageHead,
            publicFooter
        },
        data() {
            return {
                show: 1,
                boxClose:false,
                audioPlay:false,
                villageId:this.$route.query.vid,
                villageArr:[]
            }
        },
        created(){
            this.boxClose = window.innerWidth<992;
        },
        mounted() {
            this.setVillageBanner();
            this.getVillageData();
        },
        methods: {
            //获取当前村子数据
            getVillageData:function(){
                axios.get("../../static/data/villagePage.json",{
                    id:this.villageId
                }).then(res=>{
                    this.villageArr = res.data
                })
            },
            // 初始化banner
            setVillageBanner: function () {
                let villageThumbs = new Swiper('.village-thumbs', {
                    observer: true,
                    spaceBetween: 0,
                    slidesPerView: 4,
                    watchSlidesVisibility: true,//防止不可点击
                });

                let villageBanner = new Swiper('.village-banner', {
                    observer: true,
                    autoplay:true,
                    navigation: {
                        nextEl: '.swiper-button-next',
                        prevEl: '.swiper-button-prev',
                    },
                    thumbs: {
                        swiper: villageThumbs,
                    }
                });

            },
            // 播放音乐
            playAudio:function(){
                let audio = document.querySelector("audio");
                if(this.audioPlay){
                    audio.pause();
                    this.audioPlay = false
                }else{
                    audio.play();
                    this.audioPlay = true
                }
            },
            // 播放视频
            showModal() {
                this.$bvModal.show("video-modal");
                setTimeout(function(){
                    document.querySelector(".village-home-video").play();
                },200);
            },
        }
    }
</script>
<style src="../assets/css/villageHome.css" scoped>
</style>
<style>
  .village-home-video{
    width: 100%;
    height: 100%;
    object-fit: cover;
  }

  .village-home-video:focus{
    border:none;
    outline: none;
  }
</style>
