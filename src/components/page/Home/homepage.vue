<template>
  <div class="homepage">
    <div class="banner">
      <div class="banner_mask">
        <div class="banner_avatar">
          <img :src="require('@/assets/img/girl.jpg')" alt="" />
          <ul>
            <li>{{ userInfo.login }}</li>
            <li>ID {{ userInfo.id }}</li>
          </ul>
        </div>
      </div>
    </div>
    <div class="main">
      <div class="main_left">
        <ul>
          <li
            v-for="(item, index) in linkList"
            :key="index"
            @click="handleLink(item.href)"
          >
            {{ item.title }}
          </li>
        </ul>
      </div>
      <div class="main_right">
        <el-form ref="form" :model="form" label-width="80px">
          <div id="data" name="data" class="right_title">基本资料</div>
          <el-form-item label="头像" class="avatar_warp">
            <el-upload
              class="avatar-uploader"
              action="https://jsonplaceholder.typicode.com/posts/"
              :show-file-list="false"
              :on-success="handleAvatarSuccess"
              :before-upload="beforeAvatarUpload"
            >
              <img :src="imageUrl" class="avatar" />
              <i class="iconfont iconhuaban1fuben16"></i>
            </el-upload>
          </el-form-item>
          <el-form-item label="昵称">
            <el-input v-model="form.login"></el-input>
          </el-form-item>
          <el-form-item label="性别">
            <el-radio-group v-model="form.gender">
              <el-radio label="男"></el-radio>
              <el-radio label="女"></el-radio>
              <el-radio label="保密"></el-radio>
            </el-radio-group>
          </el-form-item>
          <el-form-item label="现居">
            <el-select
              v-model="form.province"
              @change="provinceChange"
              placeholder="请选择省份"
            >
              <el-option
                v-for="(item, index) in allList"
                :key="index"
                :label="item.provinceName"
                :value="item.provinceName"
              ></el-option>
            </el-select>
            <el-select
              class="mu_select"
              v-model="form.city"
              @change="cityChange"
              placeholder="请选择市区"
            >
              <el-option
                v-for="(item, index) in cityList"
                :key="index"
                :label="item.citysName"
                :value="item.citysName"
              ></el-option>
            </el-select>
          </el-form-item>
          <div id="information" name="information" class="right_title">
            个人信息
          </div>
          <el-form-item label="手机号">
            <el-input v-model="form.mobile_phone"></el-input>
          </el-form-item>
          <el-form-item label="QQ">
            <el-input v-model="form.qq"></el-input>
          </el-form-item>
          <el-form-item label="行业">
            <el-select
              class="mu_industry"
              v-model="form.industry"
              placeholder="请选择行业"
            >
              <el-option label="IT" value="shanghai"></el-option>
              <el-option label="医疗" value="beijing"></el-option>
            </el-select>
          </el-form-item>
          <el-form-item label="职业">
            <el-input v-model="form.occupation"></el-input>
          </el-form-item>
          <div id="privacy" name="privacy" class="right_title">隐私设置</div>
          <el-form-item label="姓名">
            <el-input v-model="form.name"></el-input>
          </el-form-item>
          <el-form-item label="身份证">
            <el-input v-model="form.idCard"></el-input>
          </el-form-item>
          <el-form-item class="mu_button">
            <el-button type="primary" @click="handleSubmit()"
              >立即创建</el-button
            >
          </el-form-item>
        </el-form>
      </div>
    </div>
    <Footer :colorConfirm="colorConfirm" />
  </div>
</template>

<script>
import Footer from "../../common/Footer";
import cityList from "../../../api/city.json";
import gql from "graphql-tag";
var EditUserGql = gql`
  mutation editUserGql(
    $id: bigint!
    $login: String!
    $gender: String!
    $city: String!
    $mobile_phone: String!
    $qq: String!
    $occupation: String!
    $name: String!
  ) {
    update_users(
      where: { id: { _eq: $id } }
      _set: {
        login: $login
        gender: $gender
        city: $city
        mobile_phone: $mobile_phone
        qq: $qq
        occupation: $occupation
        name: $name
      }
    ) {
      affected_rows
      returning {
        avatar
        cid
        city
        created_at
        email
        encrypted_password
        first_name
        gender
        id
        last_login_at
        last_login_location
        last_name
        login
        mobile_phone
        name
        nickname
        occupation
        qq
        remember_created_at
        reset_password_sent_at
        reset_password_token
        updated_at
      }
    }
  }
`;
export default {
  components: {
    Footer,
  },
  data() {
    return {
      colorConfirm: "#F5F5F5",
      linkList: [
        {
          href: "#data",
          title: "基本资料",
        },
        {
          href: "#information",
          title: "个人信息",
        },
        {
          href: "#privacy",
          title: "隐私设置",
        },
      ],
      form: {
        login: "",
        gender: "",
        province: "",
        city: "",
        mobile_phone: "",
        qq: "",
        occupation: "",
        name: "",
        idCard: "",
      },
      userInfo: {},
      allList: [],
      cityList: [],
      imageUrl: require("@/assets/img/girl.jpg"),
    };
  },
  methods: {
    handleAvatarSuccess(res, file) {
      this.imageUrl = URL.createObjectURL(file.raw);
    },
    beforeAvatarUpload(file) {
      const isJPG = file.type === "image/jpeg";
      const isLt2M = file.size / 1024 / 1024 < 2;

      if (!isJPG) {
        this.$message.error("上传头像图片只能是 JPG 格式!");
      }
      if (!isLt2M) {
        this.$message.error("上传头像图片大小不能超过 2MB!");
      }
      return isJPG && isLt2M;
    },
    handleLink(href) {
      document.querySelector(href).scrollIntoView(true);
    },
    // 省级
    provinceChange(val) {
      this.form.province = val;
      this.cityList = this.allList.filter((item) => {
        return item.provinceName == val;
      })[0].citys;
    },
    // 市级
    cityChange(val) {
      this.form.city = val;
    },
    handleSubmit() {
      let city = this.form.province + "," + this.form.city;
      this.$apollo
        .mutate({
          // 更新的语句
          mutation: EditUserGql,
          // 实参列表
          variables: {
            id: this.userInfo.id,
            login: this.form.login,
            gender: this.form.gender,
            city: city,
            mobile_phone: this.form.mobile_phone,
            qq: this.form.qq,
            occupation: this.form.occupation,
            name: this.form.name,
          },
        })
        .then((response) => {
          // 输出获取的数据集
          this.$message({
            message: "编辑成功！",
            type: "success",
          });
          window.$store.commit(
            "setUserInfo",
            response.data.update_users.returning[0]
          );
        })
        .catch((err) => {
          // 捕获错误
          this.$message({
            message: "错了哦！编辑失败",
            type: "error",
          });
        });
    },
  },
  created() {
    this.allList = cityList.provinces;
    this.userInfo = window.$store.state.userInfo;
    console.log(this.userInfo);
    if (!this.userInfo.city) {
      this.form.province = "";
      this.form.city = "";
    } else {
      this.form.province = this.userInfo.city.split(",")[0];
      this.form.city = this.userInfo.city.split(",")[1];
      this.provinceChange(this.form.province);
    }
    this.form.login = this.userInfo.login;
    this.form.gender = this.userInfo.gender;
    this.form.mobile_phone = this.userInfo.mobile_phone;
    this.form.qq = this.userInfo.qq;
    this.form.occupation = this.userInfo.occupation;
    this.form.name = this.userInfo.name;
  },
};
</script>
<style lang="scss" scoped>
.homepage {
  width: 100%;
  .banner {
    width: 100%;
    height: 140px;
    background: url("../../../assets/img/backdrop.png") no-repeat center;
    background-size: cover;
    margin-bottom: 130px;
    .banner_mask {
      width: 100%;
      height: 100%;
      background: url("../../../assets/img/mask.png") no-repeat center;
      background-size: cover;
      position: relative;
      .banner_avatar {
        width: 264px;
        height: 134px;
        position: absolute;
        top: 70px;
        left: 94px;
        display: flex;
        justify-content: flex-start;
        align-items: center;
        img {
          width: 130px;
          height: 130px;
          border-radius: 50%;
          border: 4px solid #ffffff;
          margin-right: 18px;
        }
        ul {
          li:first-child {
            font-size: 28px;
            font-family: Source Han Sans CN;
            font-weight: bold;
            color: #333333;
            opacity: 1;
            margin-bottom: 24px;
          }
          li:last-child {
            font-size: 16px;
            font-family: Source Han Sans CN;
            font-weight: 400;
            color: #666666;
            opacity: 1;
          }
        }
      }
    }
  }
  .main {
    margin: 0 auto;
    width: 1200px;
    display: flex;
    padding-bottom: 100px;
    box-sizing: border-box;
    .main_left {
      width: 250px;
      border-right: 1px solid #d3d3d3;
      display: flex;
      justify-content: flex-end;
      ul {
        padding-right: 86px;
        box-sizing: border-box;
        li {
          cursor: pointer;
          font-size: 18px;
          font-family: Source Han Sans CN;
          font-weight: 400;
          margin-bottom: 45px;
          a {
            color: #000000;
          }
        }
      }
    }
    .main_right {
      width: 800px;
      .right_title {
        padding-left: 100px;
        box-sizing: border-box;
        font-size: 30px;
        font-family: Source Han Sans CN;
        font-weight: 400;
        color: #333333;
        position: relative;
        margin-top: 58px;
        margin-bottom: 46px;
      }
      .right_title::after {
        content: "";
        width: 121px;
        height: 25px;
        // background: #fff94b;
        position: absolute;
        left: 100px;
        top: 18px;
        z-index: -1;
      }
      .avatar_warp {
        /deep/ {
          .el-form-item__label,
          .el-form-item__content {
            height: 134px !important;
            line-height: 134px !important;
            font-size: 18px;
            color: #666666;
          }
          .el-upload--picture-card:hover,
          .el-upload:focus {
            color: black;
          }
          .avatar-uploader {
            width: 134px;
            height: 134px;
            border: 1px solid #dbdbdb;
            border-radius: 50%;
            position: relative;
            .el-upload {
              cursor: pointer;
              width: 40px;
              height: 40px;
              text-align: center;
              line-height: 40px;
              border-radius: 50%;
              background: #fff94b;
              position: absolute;
              right: -10px;
              bottom: 10px;
              img {
                width: 136px;
                height: 136px;
                border-radius: 50%;
                position: relative;
                top: -85px;
                left: -105px;
                z-index: -1;
              }
              i{
                position: relative;
                top: -150px;
                left: -0px;
              }
            }
          }
        }
      }
      /deep/ {
        .el-form-item--mini.el-form-item,
        .el-form-item--small.el-form-item {
          padding-left: 114px;
          box-sizing: border-box;
        }
        .el-input__inner {
          font-size: 18px;
          color: #999999;
        }
        .el-input.is-active .el-input__inner,
        .el-input__inner:focus {
          border-color: #000000;
          color: #333333;
        }
        .el-form-item--small .el-form-item__content,
        .el-form-item--small .el-form-item__label {
          height: 50px;
          line-height: 50px;
          font-size: 18px;
          color: #666666;
        }
        .el-input--small .el-input__inner {
          height: 50px;
          line-height: 50px;
        }
        .el-radio__inner {
          width: 18px;
          height: 18px;
          border-radius: 2px;
          border: 1px solid #000000;
        }
        .el-radio__label {
          font-size: 18px;
        }
        .el-radio__input.is-checked .el-radio__inner {
          background: #fff94b;
          border: 1px solid #fff94b;
        }
        .el-radio__input.is-checked + .el-radio__label {
          color: #000000;
        }
        .el-radio__input.is-checked .el-radio__inner::after {
          content: "";
          width: 10px;
          height: 5px;
          border: 1px solid black;
          border-top: transparent;
          border-right: transparent;
          text-align: center;
          display: block;
          position: absolute;
          top: 2px;
          left: 2px;
          vertical-align: middle;
          transform: rotate(-45deg);
          border-radius: 0px;
          background: none;
        }
        .el-select {
          width: 180px;
          margin-right: 20px;
        }
        .mu_select {
          width: 300px;
          margin-right: 0px;
        }
        .mu_industry {
          width: 100%;
        }
        .mu_button {
          margin-top: 110px;
          padding-left: 100px !important;
          box-sizing: border-box;
          .el-form-item__content {
            margin: 0 !important;
          }
          .el-button--small,
          .el-button--small.is-round {
            padding: 18px 34px;
            border-radius: 50px;
            background: #000000;
            font-size: 18px;
            font-weight: 400;
            color: #ffffff;
            opacity: 1;
          }
          .el-button--primary {
            border: none;
          }
          .el-button--primary:hover {
            background: #535353;
          }
        }
      }
    }
  }
}
</style>

