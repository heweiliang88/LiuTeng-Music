<template>
  <div class="header shadow">
    <div class="container flex-row">
      <div class="logo">
        <router-link :to="{ name: 'home' }" tag="a"></router-link>
      </div>
      <ul class="nav flex-row">
        <li>
          <router-link :to="{ name: 'home' }" tag="a">首页</router-link>
        </li>
        <li>
          <router-link :to="{ name: 'rank' }" tag="a">排行</router-link>
        </li>
        <li>
          <router-link :to="{ name: 'playlist' }" tag="a">歌单</router-link>
        </li>
        <li>
          <router-link :to="{ name: 'singer' }" tag="a">歌手</router-link>
        </li>
        <li>
          <router-link :to="{ name: 'video' }" tag="a">视频</router-link>
        </li>
        <li>
          <router-link :to="{ name: 'mv' }" tag="a">MV</router-link>
        </li>
      </ul>
      <div class="search" @click="openSearchPop">
        <i class="iconfont nicesearch-o"></i>
      </div>
      <div class="userbox">
        <div class="line"></div>
        <div class="is-login flex-row" v-if="loginStatu">
          <el-avatar class="avatar" :src="userInfo.avatarUrl"></el-avatar>
          <el-dropdown trigger="click" @command="handleCommand">
            <span class="el-dropdown-link">
              {{ userInfo.nickname }}
              <i class="el-icon-arrow-down el-icon--right"></i>
            </span>
            <el-dropdown-menu slot="dropdown">
              <el-dropdown-item icon="el-icon-user" command="personal">
                个人主页
              </el-dropdown-item>
              <!-- <el-dropdown-item icon="el-icon-medal">我的等级</el-dropdown-item> -->
              <!-- <el-dropdown-item icon="el-icon-setting"
                >个人设置</el-dropdown-item
              > -->
              <!-- // 退出登录删除cookie -->
              <el-dropdown-item
                divided
                icon="el-icon-switch-button"
                command="home"
              >
                退出登录
              </el-dropdown-item>
            </el-dropdown-menu>
          </el-dropdown>
          <!-- <p @click="logout">Message</p> -->
        </div>
        <div class="no-login flex-row" @click="login" v-else>登录</div>
      </div>
      <!-- 搜索弹窗口 -->
      <div class="search-wrap" :class="[searchOpenClass, searchCloseClass]">
        <div class="overlay" @click="closeSearchPop"></div>
        <div class="search-body">
          <div class="search-content">
            <div class="search-popup-cover">
              <div class="bg-effect">
                <span class="layer"></span>
              </div>
              <div class="search-form">
                <input
                  class="text"
                  type="text"
                  v-model="keyword"
                  placeholder="请输入搜索关键词并按回车键…"
                  v-on:keyup.enter="search"
                />
                <i class="iconfont nicesearch-o" @click="search"></i>
              </div>
            </div>
            <div class="search-hot" v-show="searchHistory.length">
              <div class="title flex-row">
                <i class="iconfont nicelishi"></i>
                <span>历史搜索</span>
                <p @click="clearSearch">清空</p>
              </div>
              <ul class="tags">
                <li v-for="item of searchHistory" :key="item">
                  <a class="btn flex-row" @click="tag(item)"
                    >{{ item }}
                    <span
                      class="close-dark"
                      @click.stop="deleteItem(item)"
                    ></span
                  ></a>
                </li>
              </ul>
            </div>
            <div class="search-hot">
              <div class="title flex-row">
                <i class="iconfont niceremensousuo"></i>
                <span>热门搜索</span>
              </div>
              <ul class="tags" v-if="hots.length > 0">
                <li v-for="item of hots" :key="item.first">
                  <a class="btn" @click="tag(item.first)">{{ item.first }}</a>
                </li>
              </ul>
            </div>
          </div>
          <div class="btn-close flex-center" @click="closeSearchPop">
            <span class="close-light"></span>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { mapGetters, mapActions } from 'vuex'
export default {
  data() {
    return {
      keyword: '',
      hots: [],
      historys: [],
      searchOpenClass: '',
      searchCloseClass: '',
    }
  },
  components: {},
  //监听属性 类似于data概念
  computed: {
    ...mapGetters(['userInfo', 'loginStatu', 'searchHistory']),
  },
  //监控data中的数据变化
  watch: {},
  //方法集合
  methods: {
    // 展开搜索框
    openSearchPop() {
      this.searchOpenClass = 'open'
      this.searchCloseClass = ''
    },
    // 关闭搜索框
    closeSearchPop() {
      this.searchOpenClass = ''
      this.searchCloseClass = 'close'
    },
    // 搜索
    search() {
      if (this.keyword.split(' ').join('').length !== 0) {
        this.closeSearchPop()
        this.$router.push({
          name: 'search',
          query: {
            keyword: this.keyword,
          },
        })
        this.saveSearchHistory(this.keyword)
      }
    },
    // 点击标签搜索
    tag(keyword) {
      this.saveSearchHistory(keyword)
      this.closeSearchPop()
      this.$router.push({
        name: 'search',
        query: {
          keyword,
        },
      })
    },
    // 退出登录
    // logout() {
    //   // console.log(this.$api)
    //   // try {
    //   //   let res = await this.$api.getSearchHot()
    //   //   if (res.code === 200) {
    //   //     this.hots = res.result.hots
    //   //   }
    //   // } catch (error) {
    //   //   // console.log(error);
    //   // }
    // },
    // 删除历史搜索单个
    deleteItem(item) {
      this.deleteSearchHistory(item)
    },
    // 清空搜索历史
    clearSearch() {
      this.clearSearchHistory()
    },
    // 获取热搜列表
    async getSearchHot() {
      try {
        let res = await this.$api.getSearchHot()
        if (res.code === 200) {
          this.hots = res.result.hots
        }
      } catch (error) {
        // console.log(error)
      }
    },
    // 登录
    login() {
      this.$router.push({
        name: 'login',
      })
    },
    handleCommand(command) {
      if (command == 'personal') {
        this.$router.push({
          name: 'personal',
        })
      }
      if (command == 'home') {
        this.$router.push({
          name: 'home',
        })
      }
    },
    ...mapActions([
      'saveSearchHistory',
      'deleteSearchHistory',
      'clearSearchHistory',
    ]),
  },
  //生命周期 - 创建完成（可以访问当前this实例）
  created() {},
  //生命周期 - 挂载完成（可以访问DOM元素）
  mounted() {
    this.getSearchHot()
  },
}
</script>
<style lang="stylus" scoped>
.header {
  width: 100%;
  height: 70px;
  transition: all 400ms ease-in-out;
  background-color: #fff;
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  z-index: 2000;

  .logo {
    width: 146px;
    display: flex;
    align-items: center;
    margin-right: 30px;

    a {
      width: 100%;
      display: block;
      height: 70px;
      background-position: center 10px;
      background-repeat: no-repeat;
      background-size: 100px 81px;
      background-image: url('../../../assets/images/logo_black.png');
    }

    img {
      width: 130px;
    }
  }

  .nav {
    flex: 1;

    li {
      font-size: 16px;
      height: 100%;
      padding: 0 40px;
      float: left;

      a {
        position: relative;
        transition: all 0.4s;

        &.router-link-active {
          color: $color-theme;

          &::after {
            content: '';
            position: absolute;
            background: $color-theme;
            left: 0;
            right: 0;
            bottom: -12px;
            width: 25px;
            height: 4px;
            border-radius: 50%;
            margin: 0 auto;
            opacity: 1;
          }

          &:hover {
            color: red;
          }
        }
      }
    }
  }

  .search {
    height: 100%;
    display: flex;
    align-items: center;

    i {
      font-size: 22px;
      color: #161e27;
      padding: 0 15px;
      cursor: pointer;
    }
  }

  .userbox {
    display: flex;
    align-items: center;
    cursor: pointer;
    position: relative;
    padding-left: 20px;

    .line {
      width: 1px;
      height: 22px;
      background: #e1e1e1;
      position: absolute;
      left: 0;
      top: 50%;
      margin-top: -11px;
    }

    .no-login {
      font-size: 14px;

      &:hover {
        color: $color-theme;
      }
    }

    .avatar {
      margin-right: 15px;
    }
  }

  .search-wrap {
    position: fixed;
    display: flex;
    align-items: center;
    justify-content: center;
    pointer-events: none;
    width: 100%;
    height: 100vh;
    top: 0;
    left: 0;

    .overlay {
      width: 100%;
      height: 100vh;
      top: 0;
      left: 0;
      position: absolute;
      background: rgba(120, 129, 147, 0.22);
      opacity: 0;
      -webkit-transition: opacity 0.3s;
      transition: opacity 0.3s;
      -webkit-backface-visibility: hidden;
      -webkit-transition-duration: 0.3s;
      transition-duration: 0.3s;
    }

    .search-body {
      position: relative;
      opacity: 0;
      width: 100%;
      max-width: 790px;
      animation-duration: 0.3s;
      animation-fill-mode: forwards;

      .search-content {
        background: #fff;
        position: relative;
        border-radius: 25px;
        box-shadow: 0 10px 50px -5px rgba(6, 39, 67, 0.12);
        padding: 0;
        border: 0;
        width: 100%;
        height: auto;
        overflow: hidden;

        .search-popup-cover {
          position: relative;
          overflow: hidden;
          padding: 3rem;

          .bg-effect {
            // background-image: url('../../../assets/images/personal.jpg');
            background: whilte;
            background-size: cover;
            background-repeat: no-repeat;
            background-position: center;
            position: absolute;
            top: 0;
            left: 0;
            right: 0;
            bottom: 0;
            filter: blur(8px);
            transform: scale(1.05);
            background-position: center;

            .layer {
              width: 100%;
              height: 100%;
              background-color: rgba(0, 0, 0, 0.3);
              position: absolute;
              opacity: 1;
              top: 0;
              left: 0;
              transition: opacity 0.3s ease-in-out;
            }
          }

          .search-form {
            position: relative;
            padding: 3rem 0;

            i {
              position: absolute;
              font-size: 30px;
              color: white;
              width: 100%;
              right: -339px;
              bottom: 0px;

              &:hover {
                cursor: pointer;
              }
            }

            .text {
              display: block;
              width: 100%;
              background-clip: padding-box;
              transition: border-color 0.15s ease-in-out, box-shadow 0.15s ease-in-out;
              font-size: 1.4rem;
              line-height: 1.5;
              padding: 0.625rem 0.75rem;
              height: calc(1.5em + 1.71875rem + 2px);
              text-align: center;
              font-weight: normal;
              color: #fff;
              border-color: transparent;
              background-color: rgba(255, 255, 255, 0.03);
              transition: all 0.3s ease;
              border-radius: 5px;

              &:hover {
                background-color: rgba(255, 255, 255, 0.04);
                border-color: transparent;
              }

              &:focus {
                background-color: rgba(255, 255, 255, 0.04);
                border-color: transparent;
                color: #fff;
              }

              &::placeholder {
                color: #fff;
                opacity: 1;
              }
            }
          }
        }

        .search-hot {
          padding: 1.5rem 3rem;

          .title {
            margin-bottom: 15px;

            i {
              color: $color-theme;
              font-size: 1.625rem;
              margin-right: 8px;
            }

            .nicelishi {
              font-size: 1.425rem;
            }

            span {
              font-size: 15px;
              flex: 1;
            }

            p {
              color: $color-theme;
              cursor: pointer;
            }
          }

          .tags {
            width: 100%;
            margin: 0 -0.25rem;
            display: flex;
            flex-wrap: wrap;
            align-items: center;

            li {
              padding: 0.25rem;
              cursor: pointer;

              .btn {
                display: flex;
                font-weight: 400;
                color: #6D7685;
                background-color: #f4f4f5;
                text-align: center;
                vertical-align: middle;
                user-select: none;
                border: 1px solid transparent;
                font-size: 0.75rem;
                padding: 0.3125rem 0.75rem;
                line-height: 1.5;
                border-radius: 0.25rem;
                transition: color 0.15s ease-in-out, background-color 0.15s ease-in-out, border-color 0.15s ease-in-out, box-shadow 0.15s ease-in-out;
                border-radius: 4px;

                .close-dark {
                  display: inline-block;
                  background-image: url('../../../assets/images/close-dark.svg');
                  background-size: contain;
                  background-position: center;
                  background-repeat: no-repeat;
                  vertical-align: middle;
                  width: 14px;
                  height: 14px;
                  margin-left: 8px;
                  opacity: 0.7;
                }

                &:hover {
                  color: #161E27;

                  .close-dark {
                    opacity: 1;
                  }
                }
              }
            }
          }
        }
      }

      .btn-close {
        position: absolute;
        bottom: -50px;
        left: 0;
        width: 100%;
        z-index: 99;
        cursor: pointer;
        text-align: center;

        .close-light {
          display: inline-block;
          background-image: url('../../../assets/images/close.svg');
          background-size: contain;
          background-position: center;
          background-repeat: no-repeat;
          vertical-align: middle;
          width: 28px;
          height: 28px;
        }
      }
    }

    &.open {
      z-index: 1031;

      .overlay {
        opacity: 1;
        pointer-events: auto;
        -webkit-backdrop-filter: blur(10px);
        backdrop-filter: blur(10px);
      }

      .search-body {
        pointer-events: auto;
        -webkit-animation-name: tips-open;
        animation-name: tips-open;
      }
    }

    &.close {
      .search-body {
        -webkit-animation-name: tips-close;
        animation-name: tips-close;
      }
    }

    @keyframes tips-open {
      0% {
        opacity: 0;
        -webkit-transform: translate3d(0, 50px, 0);
        transform: translate3d(0, 50px, 0);
      }

      100% {
        opacity: 1;
        -webkit-transform: translate3d(0, 0, 0);
        transform: translate3d(0, 0, 0);
      }
    }

    @keyframes tips-close {
      0% {
        opacity: 1;
        -webkit-transform: translate3d(0, 0, 0);
        transform: translate3d(0, 0, 0);
      }

      100% {
        opacity: 0;
        -webkit-transform: translate3d(0, 50px, 0);
        transform: translate3d(0, 50px, 0);
      }
    }
  }
}
</style>
