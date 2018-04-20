<template>
  <div style="width:100%;height:100%;">
      <swiper :options="swiperOption" style="height:100%;">
        <swiper-slide class="slide">
            <img class="topbj" src="../assets/index.jpg" alt="" />
        </swiper-slide>
        <swiper-slide :key="index" v-for="index in maxOrderNumber" class="slide" style="position:relative;">
            <!-- <img style="position:absolute;left:10%;width:80%;top:40px;z-index:10;" src="../assets/title.png" alt="" /> -->
            <!-- <div style="position:absolute;left:0;top:0;width:100%;0:20px;z-index:10;height:100px;background:-webkit-gradient(linear, 0 0, 0 bottom, from(#000000), to(rgba(0, 0, 255, 0))); ">
              <p style="color:#fff;line-height:24px;margin-top:30px;margin-left:20px;">2018年国安城市</p>
              <p style="color:#fff;line-height:24px;font-weight:600;margin-left:20px;">第二届员工文化艺术节回顾</p>
            </div> -->
            <img style="width:100%;position:absolute;left:0;" :src="getProgramDiscByImg(index)" alt="" />
           
            <div class="textarea">
              <div style="width:100%;height:100%;position:relative;">
                <div class="smallDiv">
                  <span>《</span>{{getProgrDisName(index)}}<span>》</span>
                </div>
                 {{getProgramDiscByOrder(index)}}
              </div>
               
            </div>
            <swiper :options = "swiperOption1" class="nameList">
              <swiper-slide class="smallslide" :key="index1" v-for="(items,index1) in swiperListArr" v-if="items.Ordernum === index" >
                <!-- <img class="thumbnail" v-show="items.Headimgurl !== ''" :src="items.Headimgurl + '?imageView2/1/w/200/h/200'" alt=""  @click="liClick(items, index1)" /> -->
                <img class="thumbnail" v-show="items.Headimgurl !== ''" :src="items.Headimgurl" alt=""  @click="liClick(items, index1)" />
              </swiper-slide>
            </swiper>
        </swiper-slide>
        <swiper-slide class="slide" style="width:100%;height:100%;">
            <img class="topbj" src="../assets/last.jpg" alt="" />
        </swiper-slide>
      </swiper>
      <div class="arrow" id="arrowSlide"></div>
      <div class="market" v-show="marketShow" @click="closeClick"></div>
      
      <div class="bigPhoto" v-show="marketShow">
        <div class="close"  v-show="marketShow" @click="closeClick"></div>
        <swiper v-if="swiperOption2" :options="swiperOption2" style="position:relative;">
          <swiper-slide class="theslide" :key="index" v-for="(items,index) in arr" style="position:relative;">
            <div class="imgDiv">
              <!-- <div class="pop-image" :style="{backgroundImage: `url(${items.Headimgurl}?imageView2/2/w/600/h/800)`}"></div> -->
              <div class="pop-image" :style="{backgroundImage: `url(${items.Headimgurl}`}"></div>
              <div class="nameInfo">
                <div style="padding-top:3px;padding-left:10px;font-weight:bold;color:#fff;font-size:20px;"><span style="font-weight:600;">{{items.Membername}}</span><span style="font-size:14px;padding-left:10px;">{{items.Membertitle}}</span></div>
              </div>
              
            </div>
            <div class="nameInfoComP">
                  <p style="font-size:14px;font-weight:500;">{{items.Companyname}}</p>
                  <p style="font-size:12px;">{{items.Memberlike}}</p>
              </div>
          </swiper-slide>
        </swiper>
      </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      textareaVal: "",
      marketShow: false,
      itemPhoto: "",
      swiperListArr: [],
      maxOrderNumber: 0,
      swiperOption: {
        direction: "vertical",
        slidesPerView: 1,
        spaceBetween: 30,
        mousewheel: true,
        pagination: {
          el: ".swiper-pagination",
          clickable: true
        },
        navigation: {
          nextEl: ".arrow",
          prevEl: ".swiper-button-prev"
        }
      },
      swiperOption1: {
        slidesPerView: 3,
        spaceBetween: 0,
        pagination: {
          el: ".swiper-pagination",
          clickable: true
        }
      },
      swiperOption2: null,
      name: "",
      role: "",
      Membertitle: "",
      Memberlike: "",
      xuanyu: "",
      company: "",
      arr: []
    };
  },
  methods: {
    // 照片列表的点击事件
    liClick(item, index) {
      this.arr = this.swiperListArr.filter(items => {
        return items.Ordernum == item.Ordernum;
      });
      let num = this.arr.findIndex(i => {
        return i.Headimgurl == item.Headimgurl;
      });

      this.swiperOption2 = null;
      this.swiperOption2 = {
        spaceBetween: 30,
        initialSlide: num,
        pagination: {
          el: ".swiper-pagination",
          clickable: true
        }
      };
      this.itemPhoto = item.Headimgurl;
      this.name = item.Membername;
      this.Memberlike = item.Memberlike;
      this.Membertitle = item.Membertitle;
      this.xuanyu = item.Programname;
      this.company = item.Companyname;
      this.marketShow = true;
    },
    // 关闭按钮的点击事件
    closeClick() {
      this.marketShow = false;
      this.swiperOption2 = null;
      this.arr = [];
    },
    // 数据加载接口
    photoOnLoad() {
      let geturl = "http://mt.guoanfamily.com/ebook/ebookquery";
      this.$http.get(geturl).then(
        response => {
          this.swiperListArr = response.body.Data;
          this.maxOrderNumber = this.swiperListArr[
            this.swiperListArr.length - 2
          ].Ordernum;
          // console.log("=====",this.maxOrderNumber, this.swiperListArr);
        },
        response => {
          this.showalert(response.Msg);
        }
      );
    },

    //根据order获取活动描述
    getProgramDiscByOrder(order) {
      return this.swiperListArr.find(item => {
        return item.Ordernum === order;
      }).Programdisc;
    },
    //  根据order获取图片
    getProgramDiscByImg(order) {
      return this.swiperListArr.find(item => {
        return item.Ordernum === order;
      }).Bgimg;
    },
    // 根据order获取节目名称
    getProgrDisName(order) {
      return this.swiperListArr.find(item => {
        return item.Ordernum === order;
      }).Programname;
    }
  },
  mounted() {
    this.photoOnLoad();
  }
};
</script>

<style lang="less" scoped>
.smallDiv {
  text-align: center;
  line-height: 40px;
  font-weight: 600;
  color: #fff;
  height: 40px;
  background: #d7000f;
  position: absolute;
  left: -50px;
  top: -50px;
  z-index: 5;
  font-size:16px;
}
.imgDiv {
  width: 250px;
  height: 350px;
  display: table-cell;
  vertical-align: middle;
  text-align: center;
  position: relative;
}
.arrow {
  position: fixed;
  left: 0;
  // top: 0;
  bottom: 20px;
  right: 0;
  margin: auto;
  background: url("../assets/arrow.png") no-repeat center;
  background-size: 100%;
  width: 40px;
  height: 40px;
  z-index: 8;
  animation: scals 0.5s infinite linear alternate;
}
@keyframes scals {
  0% {
    transform: translateY(-5px);
  }
  50% {
    transform: translateY(0px);
  }
  100% {
    transform: translateY(5px);
  }
}
.slide {
  // margin-bottom: 0 !important;
  width: 100%;
  height: 100%;
  .topbj {
    width: 100%;
  }
  .textarea {
    width: 82%;
    position: absolute;
    right: 15px;
    bottom: 180px;
    opacity: 0.8;
    background: #000;
    color: #ffffff;
    padding: 20px 10px 10px 10px;
    font-size: 12px;
    border-radius: 3px;
  }
  .inputArea {
    width: 100%;
    height: 100%;
    border: none;
    padding: 10px;
    font-size: 16px;
  }
  .nameList {
    padding-left:15px;
    width: 100%;
    overflow: hidden;
    position: absolute;
    left: 0;
    bottom: 80px;
    background:-webkit-gradient(linear, 0 0, 0 bottom, from(#000000), to(rgba(0, 0, 255, 0)));
    .smallslide {
      height: 80px !important;
      width: 65px !important;
      margin-right: 15px !important;
      text-align: center;
      .swiper-wrapper {
        transform: none;
      }
      .thumbnail {
        display: inline-block;
      }
      img {
        // width: 100%;
        width: 100%;
      }
    }
    
  }
}
/* 遮罩层 */

.market {
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.85);
  position: fixed;
  left: 0;
  bottom: 0;
  top: 0;
  right: 0;
  margin: auto;
  z-index: 10;
}

.bigPhoto {
  // background:red;
  width: 76%;
  bottom: 15%;
  z-index: 11;
  position: absolute;
  top: 10%;
  left: 13%;
  text-align: center;
  .xuanyu {
    width: 100%;
    height: 100px;
    div {
      font-size: 30px;
      color: #fff;
      text-align: center;
      font-weight: 500;
    }
    span {
      color: #fff;
      float: right;
      font-size: 20px;
    }
  }
}
.nameInfo {
  width: 60%;
  // height: 90px;
  // background: url("../assets/aletName.png") no-repeat center;
  // background-size: 100%;
  background: #d7000f;
  position: absolute;
  bottom: 0px;
  right: -35px;
  font-style: italic;
  text-align: left;
}
.nameInfoComP {
  bottom: 0;
  background: none;
  font-size: 12px;
  padding-left: 10px;
  color: #fff;
  width: 50%;
  text-align: left;
  margin-top: 10px;
  float: right;
  color:#fff;
}
.close {
  width: 30px;
  height: 30px;
  background: url("../assets/close.png") no-repeat center;
  background-size: 100%;
  position: absolute;
  right: -20px;
  top: -5px;
  z-index: 11;
}

.pop-image {
  width: 250px;
  height: 350px;
  margin: 0 auto;
  background-size: 100% 100%;
  background-repeat: no-repeat;
  background-position: center;
}
</style>
