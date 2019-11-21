<template>
  <div class="box">
    <div class="clearfix head-box">
      <div class="box">
        <h1 class="float-left logo">
          <a href="/">
            <img src="../../static/images/logo.png" v-if="show===1||show>10" alt="logo" title="主页">
            <img src="../../static/images/logoBlack.png" v-if="show!==1&&show<10" alt="logo" title="主页">
          </a>
        </h1>
        <ul class="float-left list-inline head-list-box">
          <li class="list-inline-item head-list" :class="{active:show === 1}"><a href="/">首页</a></li>
          <li class="list-inline-item head-list" :class="{active:show === 2}"><a href="/village">村落</a></li>
          <li class="list-inline-item head-list" :class="{active:show === 3||show === 31}"><a href="/information-home">资讯</a>
          </li>
          <li class="list-inline-item head-list" :class="{active:show === 4}"><a href="/serve">服务</a></li>
        </ul>
        <div class="float-right login-btn">
          <a href="/login">
            登录 / 注册
          </a>
        </div>
      </div>
    </div>

    <div class="broadside-box" :class="{'broadside-box-center':rightBtn}">
      <div class="broadside-list">
        <a href="/panorama">
          <i class="list-icon"></i>
          <p class="list-text">全景</p>
        </a>
      </div>

      <div class="broadside-list">
        <a href="">
          <i class="list-icon"></i>
          <p class="list-text">微信</p>
        </a>
      </div>

      <div class="broadside-list">
        <a href="/consult">
          <i class="list-icon"></i>
          <p class="list-text">咨询</p>
        </a>
      </div>

      <div class="broadside-list">
        <a href="">
          <i class="list-icon"></i>
          <p class="list-text">分享</p>
        </a>
      </div>
    </div>
  </div>
</template>

<script>
    export default {
        props: {
            show: {
                type: Number,
                required: true
            },
            rightBtn: {
                type: Boolean,
                required: true
            }
        }
    }
</script>

