<template>
  <div class="header">
    <div class="header_logo" @click="handleIndex">
      <div class="logo_warp">
        <img :src="require('@/assets/img/header_logo.png')" alt="" />
        <span>二木素材</span>
      </div>
    </div>
    <div class="header_nav">
      <li
        @click="handleChange(item.id, item.path, item.name)"
        :class="item.id == navActive ? 'active' : ''"
        v-for="(item, index) in nav"
        :key="index"
      >
        {{ item.name }}
      </li>
    </div>
    <div class="header_right">
      <div class="search">
        <el-select v-model="value" @change="selectChange">
          <el-option
            v-for="item in searchNav"
            :key="item.name"
            :label="item.name"
            :value="item.name"
          >
          </el-option>
        </el-select>
        <i class="iconfont iconhuaban1fuben15"></i>
        <!-- <img :src="require('@/assets/img/dropdown_bottom.png')" alt="" /> -->
        <input
          type="text"
          v-model="searchVal"
          @keyup.enter="handleSearch"
          placeholder="输入想要搜索的内容"
        />
        <div class="search_buttom" @click="handleSearch()">
          <i class="iconfont iconhuaban1fuben71"></i>
        </div>
      </div>
      <div class="header_avatar" v-if="Object.keys(this.userInfo).length">
        <div class="avatar_warp">
          <img :src="require('@/assets/img/girl.jpg')" alt="" />
          <ul>
            <li
              v-for="(item, index) in dataList"
              :key="index"
              @click="handleNav(item.path, item.name)"
            >
              <i :class="item.icon"></i>
              <!-- <img :src="item.url" alt="" /> -->
              {{ item.name }}
            </li>
          </ul>
        </div>
      </div>
      <div v-else class="loginRegister">
        <span @click="handleLogin">登录</span>/
        <span @click="handleRegister">注册</span>
      </div>
    </div>
  </div>
</template>

<script>
import gql from "graphql-tag";
export default {
  data() {
    return {
      //   userInfo: window.$store.state.userInfo,
      className: 0,
      value: "全部",
      dropdown: "全部",
      dropdownId: 0,
      searchVal: "",
      nav: [
        {
          id: 0,
          path: "/index",
          name: "首页",
        },
      ],
      searchNav: [
        {
          id: 0,
          name: "全部",
        },
      ],
      dataList: [
        {
          path: "/homepage",
          icon: "iconfont iconhuaban1fuben31",
          name: "个人主页",
        },
        {
          path: "/personal",
          icon: "iconfont iconhuaban1fuben9",
          name: "我的收藏",
        },
        {
          path: "/personal",
          icon: "iconfont iconhuaban1fuben12",
          name: "浏览记录",
        },
        {
          path: "/personal",
          icon: "iconfont iconhuaban1fuben11",
          name: "下载记录",
        },
        {
          path: "/personal",
          icon: "iconfont iconhuaban1fuben13",
          name: "退出登录",
        },
      ],
    };
  },
  computed: {
    navActive() {
      return window.$store.state.navActive;
    },
    userInfo() {
      return window.$store.state.userInfo;
    },
  },
  methods: {
    // 登录弹框
    handleLogin() {
      this.$root.$children[0].showLogin(true);
    },
    handleRegister() {
      this.$root.$children[0].showRegister(true);
    },
    handleChange(id, path, name) {
      this.className = id;
      if (path == "/index") {
        this.$router.push({
          path: path,
        });
      } else {
        this.$router.push({
          path: path,
          query: { id: id },
        });
      }
      window.$store.commit("setNavActive", id);
    },
    handleIndex() {
      this.$router.push({
        path: "/index",
      });
    },
    handleNav(path, name) {
      if (name == "个人主页") {
        this.$router.push({
          path: path,
        });
      } else if (name == "退出登录") {
        localStorage.removeItem("userInfoStore");
        localStorage.removeItem("folderStore");
        this.$cookies.remove("u");
        window.$store.commit("setUserInfo", {});
        window.$store.commit("setFolder", []);
        this.$router.push({
          path: "/index",
        });
      } else {
        this.$router.push({
          path: path,
          query: { name: name },
        });
      }
    },
    selectChange(val) {
      this.dropdown = val;
      this.dropdownId = this.searchNav.filter((item) => {
        return item.name == val;
      })[0].id;
    },
    handleSearch() {
      this.$router.push({
        path: "/search",
        query: { id: this.dropdownId, searchVal: this.searchVal },
      });
    },
    // 获取行业
    handleGetCategories() {
      this.$apollo
        .query({
          query: gql`
            {
              categories {
                id
                name
              }
            }
          `,
          fetchPolicy: "no-cache",
        })
        .then((data) => {
          for (let i in data.data.categories) {
            this.nav.push({
              id: data.data.categories[i].id,
              path: "/formwork",
              name: data.data.categories[i].name,
            });
            this.searchNav.push({
              id: data.data.categories[i].id,
              name: data.data.categories[i].name,
            });
          }
          window.$store.commit("setCategoriesId", data.data.categories);
        });
    },
  },
  created() {
    this.handleGetCategories();
  },
};
</script>
<style lang="scss" scoped>
.header {
  width: 100%;
  height: 99px;
  background: white;
  border-bottom: 1px solid #e5e5e5;
  display: flex;
  justify-content: space-between;
  .header_logo {
    cursor: pointer;
    display: flex;
    justify-content: center;
    align-items: center;
    padding-left: 60px;
    box-sizing: border-box;
    .logo_warp {
      display: flex;
      align-items: center;
      span {
        margin-left: 16px;
        font-size: 24px;
        font-weight: bold;
        color: #333333;
        opacity: 1;
      }
    }
  }
  .header_nav {
    display: flex;
    align-items: center;
    li {
      cursor: pointer;
      border-radius: 44px;
      line-height: 44px;
      padding: 0 25px;
      box-sizing: border-box;
      font-size: 20px;
      font-weight: 400;
      color: #333333;
      margin-right: 10px;
    }
    li:hover {
      background: #fcf89f;
    }
    .active {
      background: #fff94b;
      position: relative;
    }
    .active::after {
      content: "";
      width: 41px;
      height: 4px;
      background: #333333;
      position: absolute;
      top: 68px;
      left: 50%;
      transform: translateX(-50%);
    }
  }
  .header_right {
    display: flex;
    align-items: center;
    padding-right: 40px;
    box-sizing: border-box;
    .search {
      height: 50px;
      background: #ffffff;
      border: 1px solid #333333;
      opacity: 1;
      border-radius: 56px;
      margin-right: 30px;
      display: flex;
      align-items: center;
      position: relative;
      /deep/ {
        .el-select {
          cursor: pointer;
          width: 115px;
          padding: 0 15px;
          box-sizing: border-box;
          .el-input__inner {
            border: none;
          }
          .el-input {
            overflow: hidden;
            text-overflow: ellipsis;
            white-space: nowrap;
          }
          .el-input--small .el-input__inner {
            border-radius: 56px;
            font-size: 18px;
            font-weight: 400;
            color: #333333;
            padding: 0;
          }
          .el-input__suffix {
            display: none;
          }
        }
      }
      > i {
        position: absolute;
        left: 90px;
      }
      > input {
        padding-right: 10px;
        box-sizing: border-box;
        width: 190px;
        height: 50px;
        border: none;
        outline: none;
        font-size: 18px;
        font-weight: 400;
        color: #333333;
      }
      > input::-webkit-input-placeholder {
        font-size: 18px;
        font-weight: 400;
        color: #999999;
        opacity: 1;
      }
      .search_buttom {
        cursor: pointer;
        width: 49px;
        height: 49px;
        border-radius: 50%;
        display: flex;
        justify-content: center;
        align-items: center;
        i {
          font-size: 20px;
          font-weight: bold;
        }
      }
      .search_buttom:hover {
        background: #fff94b;
      }
    }
    .header_avatar {
      width: 60px;
      height: 60px;
      .avatar_warp {
        cursor: pointer;
        > img {
          width: 100%;
          height: 100%;
          border-radius: 50%;
        }
        ul {
          position: relative;
          z-index: 9;
          left: -100%;
          display: none;
          width: 150px;
          background: #ffffff;
          box-shadow: 0px 10px 25px rgba(0, 0, 0, 0.16);
          opacity: 1;
          border-radius: 14px;
          padding: 10px 10px;
          box-sizing: border-box;
          li {
            text-align: center;
            line-height: 40px;
            border-radius: 14px;
            // background: #fff94b;
            margin-bottom: 5px;
            font-size: 18px;
            font-family: Source Han Sans CN;
            font-weight: 400;
            color: #333333;
            i {
              font-size: 18px;
              margin-right: 9px;
            }
            img {
              width: 18px;
              height: 16px;
            }
          }
          li:hover {
            background: #fff94b;
          }
        }
      }
      .avatar_warp:hover {
        ul {
          display: block;
        }
      }
    }
    .loginRegister {
      span {
        cursor: pointer;
        font-size: 18px;
        font-weight: 400;
        color: #333333;
      }
    }
  }
}
</style>
<style lang="scss">
.el-select-dropdown__item.selected {
  color: #333333;
  font-weight: 700;
}
.el-select-dropdown__item.hover,
.el-select-dropdown__item:hover {
  background: #fff94b;
}
</style>

