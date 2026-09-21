<template>
  <div class="conPage">
    <!--  头部-->
    <div class="topBox">
      <div class="leftBox">
        <div class="avatar">
          <van-uploader :after-read="onRead">
            <img class="img"
                 :src="userInfo.img"
                 alt="">
          </van-uploader>
        </div>
        <div>
          <div class="phone">{{ userInfo.phone }}</div>
          <!-- <div class="vipBack">悦享会员</div> -->
        </div>
      </div>
      <!-- <div class="settingBox">
        <div class="setting" @click="toStting('/setting')">
          <img class="img" src="@/assets/mine/sz.png" alt="">
        </div>
        <div class="setting" @click="scanQRCode">
          <img class="img" src="@/assets/mine/sys.png" alt="">
        </div>
      </div> -->
    </div>
    
    <div class="centerBox">
       <div class="coupons1">
        <div class="couponsTitle" @click="scanQRCode">扫码绑定  
          <img class="img" src="http://yxfmm.bjyxfl.com/imgs/add/sys.png"   alt="" style="width: 20px;margin-left: 6px;">
         </div>
        <div class="couponsBox" style="justify-content: space-between;">
          <div class="welfareBox" style="width: 44%;" @click="toFreea()">
            <div>
              <div class="welfareCard" style="white-space: nowrap">福利卡</div>
              <div class="none" style="text-align: center;white-space: nowrap;" v-if="userInfo.card_num != 0">{{
                userInfo.card_num }}张福利卡</div>
              <div class="none" style="text-align: center;white-space: nowrap;" v-else>暂无福利卡</div>
            </div>
            <div class="welfare" style="width: 39px;"  >
              <img class="img" src="http://yxfmm.bjyxfl.com/imgs/add/flk.png" alt="">
            </div>
          </div>
          <div class="lines"></div>
          <a @click="toOrder(4)">
            <div class="welfareBox">
              <div>
                <div class="welfareCard">在线客服</div>
                <div class="none">全年无休(9:00-21:00)</div>
              </div>
              <div class="welfare">
                <img class="img" src="http://yxfmm.bjyxfl.com/imgs/add/kf.png" alt="">
              </div>
            </div>
          </a>

        </div>
      </div>
      <!-- <div class="freeaBox" @click="toFreea">
        <div class="leftBox">
          <div class="vipIcon">
            <img class="img" src="../../assets/mine/hz.png" alt="">
          </div>
          <div class="freeaText">我的福利卡</div>
        </div>
        <div class="iconBox">
          <van-icon name="arrow"/>
        </div>
      </div> -->
      <!--      我的订单-->
      <div class="coupons orderMax" style="margin-top: 10px;border-radius: 8px">
        <div class="myOrder">
          <div class="couponsTitle">我的订单</div>
          <div class="allOrder" @click="allOrder">
            <div>全部</div>
            <div class="arrow">
              <van-icon size="12px" name="arrow"/>
            </div>
          </div>
        </div>
        <!--        状态-->
        <div class="obligationMax">
          <div class="obligationBox" v-for="item in orderIcon" :key="item.id" @click="toOrder(item.id)">
            <div class="obligationImg">
              <img class="img" :src="item.icon" alt="">
              <div class="point" v-if="item.type&&item.id==1"></div>
              <div class="num" v-if="item.type&&item.id==2">{{ item.type }}</div>
            </div>
            <div class="obligationText">{{ item.text }}</div>
          </div>
        </div>
      </div>
      <!--  功能-->
      <div class="coupons orderMax" style="margin-top: 10px;border-radius: 8px">
        <div class="obligationMax" style="padding: 0px 10px 0px 10px ;">
          <div class="obligationBox"  v-for="item in mineFun" :key="item.id"
               @click="toFun(item.id,item.path)">
            <div class="obligationImg" style="width: 24px;height: 24px;">
              <img class="img" :src="item.icon" alt="">
            </div>
            <div class="obligationText" style="white-space: nowrap;" >
              {{ item.text }}
            </div>
          </div>
        </div>
      </div>
    </div>
    <div>
<div style="padding: 0 10px 100px 10px;">
  <div class="hotmember">热门推荐 <span></span> </div>
      <NewShopList :flag="1" :shopSwiper="shopSwiper" :shopList="shopList" v-if="shopList.length>0"></NewShopList>

</div>
    </div>
    <!--  tabbar v-if="!$store.state.film"-->
<!--    <NavigationTab  :active="4"></NavigationTab>-->
<!--    <filmTab v-if="$store.state.film==1" :active="2"></filmTab>-->
  </div>
</template>

<script>
import NewShopList from "@/components/NewShopList.vue";
import NavigationTab from "@/components/NavigationTab.vue";
import {changeAvatar, getUserInfo} from "@/api/mine";
import FilmTab from "@/components/filmTab.vue";
import wx from "weixin-js-sdk";
import {bindingcard, qrcodeBinging} from "@/api/cart";
import { getGoodPickList} from "@/api";


export default {
  name: "PersonCenter",
  components: {FilmTab, NavigationTab,NewShopList},
  data() {
    return {
      shopSwiper:[],
      shopList: [],
      pageno:1,
      orderIcon: [
        {
          id: 1,
          icon: 'http://yxfmm.bjyxfl.com/imgs/add/dfk.png',
          text: "待付款"
        },
        {
          id: 2,
          icon: 'http://yxfmm.bjyxfl.com/imgs/add/dsy.png',
          text: "待收货"
        },
        {
          id: 3,
          icon: 'http://yxfmm.bjyxfl.com/imgs/add/dpj.png',
          text: "已完成"
        },
        {
          id: 4,
          icon: 'http://yxfmm.bjyxfl.com/imgs/add/sh.png',
          text: "退款售后"
        },
      ],
      mineFun: [
        {
          id: 1,
          icon:'http://yxfmm.bjyxfl.com/imgs/add/map.png',
          text: "地址管理",
          path: "/address"
        },
        {
          id: 2,
          icon: 'http://yxfmm.bjyxfl.com/imgs/add/zfmm.png',
          text: "支付密码"
        },
        {
          id: 3,
          icon: "http://yxfmm.bjyxfl.com/imgs/add/zn.png",
          text: "使用指南",
          path: "/userGuide"
        },
        {
          id: 4,
          icon:"http://yxfmm.bjyxfl.com/imgs/add/yj.png",
          text: "意见反馈"
        },
         {
          id: 5,
          icon:"http://yxfmm.bjyxfl.com/imgs/add/tc.png",
          text: "退出登录"
        },
      ],
      userInfo: {},
      token: "",
      kefu: "",
      info: {}
    }
  },
  methods: {
    getGoodPick(pageno = this.pageno) {
      if (this.isTab) {
        this.shopList = []
      }
      this.isLoading = true
      getGoodPickList({
        pageno,
        pagesize: 10
      }).then(res => {
        this.isLoading = false
        setTimeout(() => {
          this.loadingflag = false
        }, 500)
        if (res.code == 200) {
          if (res.data.product_list.length == 0) {
            this.isScroll = true
            return
          }
          res.data.product_list.forEach(item => {
            this.shopList.push(item)
          })
          // console.log( this.shopList.length)
          this.isTab = false
          // if(this.$refs.bannerBox){
          //   setTimeout(() => {
          //     this.swiperHeight = (this.$refs.bannerBox.getBoundingClientRect().height).toFixed(0)
          //   }, 200)
          // }
        }
      })
    },
    scanQRCode() {
      let _that = this;
      wx.ready(function () {
        wx.checkJsApi({
          jsApiList: ['scanQRCode'],
          success: function (res) {
            if (res.checkResult.scanQRCode === true) {
              wx.scanQRCode({ // 微信扫一扫接口
                needResult: 1, // 默认为0，扫描结果由微信处理，1则直接返回扫描结果，
                scanType: ['qrCode', 'barCode'], // 可以指定扫二维码还是一维码，默认二者都有
                success: function (res) {
                  let url = res.resultStr // 当needResult 为 1 时，扫码返回的结果
                  let token = localStorage.getItem("token")
                  qrcodeBinging({
                    token,
                    content: url
                  }).then(res => {
                    if (res.code == 200) {
                      setTimeout(() => {
                        _that.$router.go(-1)
                        _that.$toast(res.data)
                      }, 1000)
                    } else {
                      _that.$toast(res.msg)
                    }
                  })
                }
              })
            } else {
              _that.$toast("抱歉，当前客户端版本不支持扫一扫");
            }
          },
          fail: function (res) { // 检测getNetworkType该功能失败时处理
            _that.$toast('fail' + res)
          }
        });
      });
      //错误信息
      wx.error(function (res) {
        _that.$toast("出错了：" + res.errMsg);
      });
    },
    getSign() {
      // this.$toast({message:purl,duration:0})
      //请求后端，获取微信签名信息
      let configData = {
        debug: false,
        appId: this.info.appid, // 必填，公众号的唯一标识
        timestamp: "" + this.info.time, // 必填，生成签名的时间戳
        nonceStr: this.info.nonceStr, // 必填，生成签名的随机串
        signature: this.info.signature,// 必填，签名
        jsApiList: ['scanQRCode', 'checkJsApi']
      }
      wx.config(configData);
      wx.ready(function (res) {

      });
      wx.error(function (res) {
      });
    },
    getbindingcard() {
      let purl = /(Android)/i.test(navigator.userAgent) ? location.href.split('#')[0] : window.localStorage.getItem('scanUrl');
      bindingcard({
        url: purl
      }).then(res => {
        this.info = res.data
        this.getSign()
      })
    },
    // 上传头像
    onRead(file) {
      var formdata = new FormData(); //new一个FormData
      formdata.append('img', file.file)  //把上传的图片信息放入，传给后台
      formdata.append('token', this.token)  //把上传的图片信息放入，传给后台
      formdata.append('channel_no', "12358")  //把上传的图片信息放入，传给后台
      changeAvatar(formdata).then(res => {
        if (res.code == 200) {
          this.userInfo.img = res.data
          this.$toast("修改头像成功")
        }
      })
    },
    toOrder(id) {
      if (id == 4) {
        window.location.href = this.kefu
      } else {
        this.$router.push({path: "/orderInfo", query: {tabIndex: id}})
      }

    },
    allOrder() {
      this.$router.push({path: "/orderInfo", query: {tabIndex: 0}})
    },
    //   去福利卡
    toFreea() {
      if (this.$store.state.film == 1) {
        window.location.href = this.$store.state.base + "/securitycards?token=" + localStorage.getItem("token")
      } else {
        window.location.href = this.$store.state.freea + localStorage.getItem("token")+"&cityName="+sessionStorage.getItem("cityName")+"&isFirst="+sessionStorage.getItem("isFirst")
      }
    },
    //   去支付密码
    toFun(id, path) {
      if (id == 2) {
        window.location.href = this.$store.state.base + "/payPassword"+"?token="+localStorage.getItem("token")
      } else if (id == 4) {
        // window.location.href = this.kefu
        window.location.href = this.$store.state.base+"/feedback"
      }else if (id == 5) {
           this.$dialog.confirm({
        title: '退出',
        message: '是否退出登录？',
        confirmButtonColor: 'red'
      }).then(() => {
        localStorage.removeItem("token")
        localStorage.removeItem("uid")
        this.$store.commit("settoken", "")
        this.$toast("退出登录成功")
        this.$router.options.routes.forEach(item=>{
          if(item.meta){
            if(item.meta.keep){
              item.meta.keepAlive=false
            }
          }
        })
        setTimeout(()=>{
          this.$router.replace({path: "/index"})
        },1000)
      }).catch(() => {
      })
      } 
      else {
        this.$router.push(path)
      }
    },
    toStting(path) {
      this.$router.push(path)
    },
    getUserInfo() {
      getUserInfo().then(res => {
        if (res.code == 200) {
          this.userInfo = res.data
        }
      })
    },
  },
  created() {
    this.token = localStorage.getItem("token")
    this.getUserInfo()
    this.kefu = localStorage.getItem("kefu")
    this.$store.commit("changeFilm", this.$route.query.film)
    // console.log(this.$store.state.film)
    this.getbindingcard();
    this.getGoodPick();
  }
}
</script>

<style scoped lang="less">
.conPage {
  background-color: #F0F0F0;
  min-height: calc(100vh - 50px);
  box-sizing: border-box;
}

.topBox {
  background-image: url("@/assets/mine/back.png");
  // background: linear-gradient(to bottom ,#FFEDED,#FFF9FB,#F0F0F0);
  width: 100%;
  height: 156px;
  background-size: cover;
  box-sizing: border-box;
  padding: 30px 10px 0px 10px;
  display: flex;
  align-items: flex-start;
  justify-content: space-between;

  .avatar {
    width: 60px;
    height: 60px;
    border-radius: 50%;
    overflow: hidden;
  }

  .vipBack {
    background-image: url("@/assets/mine/hy.png");
    width: 79px;
    height: 34px;
    background-size: cover;
    box-sizing: border-box;
    color: white;
    font-size: 10px;
    line-height: 34px;
    padding-left: 30px;
    white-space: nowrap;
  }

  .leftBox {
    display: flex;
    align-items: center;
    gap: 10px;

    .phone {
      font-weight: bold;
      margin-top: 5px;
      padding-left: 7px;
    }
  }

  .settingBox {
    display: flex;
    align-items: center;
    gap: 15px;
    margin-top: 5px;

    .setting {
      width: 24px;
    }
  }
}

.centerBox {
  padding: 0px 10px;
  margin-top: -40px;
//我的卡券
.coupons1 {
  background-color: white;
  border-radius: 8px;
  margin-top: 8px;
  padding: 14px;

  .couponsTitle {display: flex;align-items: center;
    font-weight: bold;
    color: #383838;
  }

  .welfare {
    min-width: 43px;
    width: 43px;
  }

  .welfareBox {
    display: flex;
    align-items: center;
    justify-content: space-between;
    //width: 42%;
    padding: 4px 10px 0px 0;
    gap: 5px;
  }

  .welfareBox:last-child {
    display: flex;
    align-items: center;
    justify-content: space-between;
    //width: 42%;
    padding: 4px 0px 0px 10px;
    gap: 5px;
  }

  .none {
    font-size: 12px;
    color: #696969;
    margin-top: 5px;
  }

  .welfareCard {
    font-weight: bold;
    font-size: 15px;
    color: #343434;
    white-space: nowrap;
  }

  .couponsBox {
    display: flex;
    align-items: center;
    padding-top: 8px;
    //justify-content: space-between;
  }

  .lines {
    width: 1px;
    background-color: #f3f3f3;
    height: 45px;
  }
}
//
  .freeaBox {
    display: flex;
    align-items: center;
    justify-content: space-between;
    background-image: url("@/assets/mine/hb.png");
    width: 100%;
    height: 60px;
    background-size: cover;
    box-sizing: border-box;
    padding: 7px 23px 0px;

    .vipIcon {
      width: 32px;
    }

    .leftBox {
      display: flex;
      align-items: center;
      gap: 5px;
      font-size: 15px;
      color: #80582B;
      font-weight: bold;
    }

    .iconBox {
      color: #80582B;
    }

    .freeaText {
      margin-top: -5px;
    }
  }

  .coupons {
    background-color: white;
    padding: 15px;
    border-bottom-left-radius: 8px;
    border-bottom-right-radius: 8px;
  }

  //我的订单
  .orderMax {
    .couponsTitle {
      font-weight: bold;
      color: #383838;
      font-size: 15px;
    }

    .myOrder {
      display: flex;
      align-items: center;
      justify-content: space-between;
    }

    .allOrder {
      color: #9D9D9D;
      display: flex;
      align-items: center;
      font-size: 13px;
    }

    .arrow {
      height: 11px;
      padding-left: 3px;
    }

    .obligationBox {
      text-align: center;
    }

    .obligationImg {
      width: 28px;
      height: 28px;
      margin: auto;
      position: relative;
    }

    .obligationText {
      margin-top: 3px;
      font-size: 13px;
      font-weight: bold;
    }

    .point {
      position: absolute;
      width: 8px;
      height: 8px;
      background-color: #ED3036;
      border-radius: 50%;
      top: 0px;
      right: 0px;
    }

    .num {
      position: absolute;
      min-width: 13px;
      height: 10px;
      border-radius: 50%;
      top: -10px;
      left: 17px;
      border: 1px solid #ed3036;
      color: #ed3036;
      font-size: 10px;
      padding: 1px 1px 3px;
    }
  }

  .obligationMax {
    display: flex;
    align-items: center;
    justify-content: space-between;
    padding: 14px 20px 0px;
  }
}

.hotmember {
  margin-top: 10px;
  background: #ffffff;
  border-radius: 8px;
  padding: 8px 15px;
  position: relative;
  font-weight: bold;
  color: #383838;

  span {
    position: absolute;
    bottom: 0;
    left: 33px;
    background-color: #FF7D2C;
    height: 4px;
    width: 30px;
    border-radius: 4px;
    display: inline-block;
  }
}
</style>