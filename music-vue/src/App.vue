<!--
 * @Descripttion: 
 * @version: 
 * @Author: heweiliang
 * @Date: 2021-04-20 15:29:23
 * @LastEditors: heweilang
 * @LastEditTime: 2021-04-23 15:57:39
-->
<template>
  <div id="app">
    <nice-header v-if="!$route.meta.isLogin"></nice-header>
    <router-view />
    <player-bar v-if="!$route.meta.isLogin"></player-bar>
    <nice-footer v-if="!$route.meta.isLogin"></nice-footer>
  </div>
</template>
<script>
  import NiceHeader from 'components/common/header/index'
  import PlayerBar from 'components/common/playerBar/index'
  import NiceFooter from 'components/common/footer/index'
  export default {
    name: 'App',
    components: {
      NiceHeader,
      PlayerBar,
      NiceFooter,
    },
  }
</script>
<style lang="stylus">
  @import './assets/styles/iconfont.css';

  .fly {
    pointer-events: none;
    position: fixed;
    z-index: 100;
  }

  .bg-fly-circle1 {
    left: 40px;
    top: 100px;
    width: 100px;
    height: 100px;
    border-radius: 50%;
    background: linear-gradient(to right, rgba(100, 84, 239, 0.07) 0%, rgba(48, 33, 236, 0.04) 100%);
    animation: move 2.5s linear infinite;
  }

  .bg-fly-circle2 {
    left: 3%;
    top: 60%;
    width: 150px;
    height: 150px;
    border-radius: 50%;
    background: linear-gradient(to right, rgba(100, 84, 239, 0.08) 0%, rgba(48, 33, 236, 0.04) 100%);
    animation: move 3s linear infinite;
  }

  .bg-fly-circle3 {
    right: 2%;
    top: 140px;
    width: 145px;
    height: 145px;
    border-radius: 50%;
    background: linear-gradient(to right, rgba(100, 84, 239, 0.1) 0%, rgba(48, 33, 236, 0.04) 100%);
    animation: move 2.5s linear infinite;
  }

  .bg-fly-circle4 {
    right: 5%;
    top: 60%;
    width: 160px;
    height: 160px;
    border-radius: 50%;
    background: linear-gradient(to right, rgba(100, 84, 239, 0.02) 0%, rgba(48, 33, 236, 0.04) 100%);
    animation: move 3.5s linear infinite;
  }

  @keyframes move {
    0% {
      transform: translateY(0px);
    }

    50% {
      transform: translateY(25px);
    }

    100% {
      transform: translateY(0px);
    }
  }
</style>