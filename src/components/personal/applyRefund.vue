<template>
  <div class="exchange">
    <!-- <common-header></common-header> -->
    <common-header v-on:childToParent="onChildClick"></common-header>
    <my-header></my-header>
    <div class="center">
      <div class="top"><span>您的位置： </span> <span>交易中心</span> > <span>我的订单 </span> > <span>申请售后</span> > <span>仅退款</span>
      </div>
      <img class="logo" src="../../assets/img/jintuikuan1.png"/>
      <div class="middle">
        <div class="left l">
          <div class="up"><span>仅退款</span></div>
          <div class="down">
            <!--<div class="shouhuo">-->
              <!--<p class="l">是否收到货：<span>*</span></p>-->
              <!--<p class="l"><input class="l" type="radio" name="is"/>未收到货</p>-->
              <!--<p class="l"><input class="l" type="radio" name="is"/>已收到货</p>-->
            <!--</div>-->
            <!-- <div class="yuanyin">退款原因：<span>*</span><select name="">
  							<option value="">请选择仅退款原因</option><option value="">大小尺寸与商品描述不相符</option><option value="">大小尺寸与商品描述不相符</option>
  						</select></div> -->
            <div class="shuoming">
              <p class="l">说明：</p><textarea maxlength="200" v-model="explain" class="l"></textarea><span>({{explain.length}}/200字)</span></div>
            <div class="jine">退款金额：<span>*</span><input readonly type="text" v-model="goods_price"/>
              元（最多<span>{{parseFloat(goods_price)}}</span>元）<span class="huise">退款金额说明</span></div>
            <div class="pingzheng">上传凭证：<span>选择凭证图片(最多三张)</span></div>
            <div class="jiahao clearfix">
              <div class="image-list l" v-show="upload_list.length != 0">
                <ul class="clearfix">
                  <li class="l" v-for="(item,index) in upload_list" :key="index">
                    <span class="close-ico" @click="del(index)"></span>
                    <img :src="URL + item" alt="">
                  </li>
                </ul>
              </div>
              <div v-show="upload_list.length < 3" class="upload-btn l">
                <input @change="imgChange($event)" accept="image/*" type="file">
                <i class="el-icon-plus"></i>
              </div>
            </div>
            <div class="but">
              <button @click="open">提交申请</button>
            </div>
          </div>
        </div>
        <div class="right r">
          <div class="xiangqing">订单详情</div>
          <div class="shangpin"><img :src="URL + img.pic_url"/><span>{{goods.title}} </span>
          </div>
          <ul class="ul">
            <li>卖 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 家：{{store_info.shop_name}}</li>
            <!--<li>联系电话：0510-87486600</li>-->
            <li>订单编号：<span class="lanse">{{$route.params.order_sn_id}}</span></li>
            <li>单 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 价：<span>{{order_info.goods_price}}</span>元*{{order_info.goods_num}}（数量）</li>
          </ul>
        </div>
      </div>
      <div class="bottom">
        <div class="jieshao"><span>服务介绍</span></div>
        <p>1.仅退款</p><span class="spa">申请条件：若您未收到货，或已收到货且与卖家达成一致不退货仅退款时，请选择“仅退款”选项。</span><span class="spa">退款流程：1.申请退款>2.卖家同意退款申请>3.退款成功</span>
        <p>2.退货退款</p><span
        class="spa">申请条件：若为商品问题，或者不想要了且与卖家达成一致退货，请选择“退货退款”选项，退货后请保留物流底单（闲置商品和未加入消保商品仅支持未收到货的退款）。</span><span
        class="spa">退货流程：1.申请退货>2.卖家发送退货地址给买家>3.买家退货并填写退货物流信息>4.卖家确认收货，退款成功</span>
        <p>3.换货</p><span class="spa">申请条件：若您与卖家协商一致换货，请选择“换货”选项。退货后请保留物流底单</span><span class="spa">换货流程：1.申请换货>2.卖家发送退货地址给买家>3.卖家线下完成换货>4.买家线上确认完成</span>
      </div>
    </div>
    <foot-com></foot-com>
  </div>
</template>

<script>
  import myHeader from './header/myHeader.vue' //个人中心的头部
  export default {
    data() {
      return {
        img:{},
        store_info:{},
        order_info:{},
        goods:{},
        goods_num:0,
        goods_price:0,
        configInfo: {},
        upload_list: [],
        explain:'',
        fromChild: '',
				headParams: {
					title: sessionStorage.getItem('titleKey'),
					description: sessionStorage.getItem('updateDescription'),
					keywords: sessionStorage.getItem('contentKey'),
					link:sessionStorage.getItem('pcfavicon')         
				}
        };
    },
    head: {
        meta: function(){
            return [
                { name: 'title', content: this.headParams.title, id: 'desc' },
                { name: 'description', content: this.headParams.description, id: 'desc1' },
                { name: 'keywords', content: this.headParams.keywords, id: 'desc2' },
            ]
        },
        link: function(){
          return [
            { rel: 'shortcut icon', href: 'imgRequest'+this.headParams.link, id:'pcLink'},
          ]  
        }
    },
    components: {
      myHeader
    },
    created() {
      this.getImageConfig();
      this.getFootData();
			this.getFavIcon(); 
    },
    mounted(){
      if(this.$route.params.type == 1){
        this.orderInfo();
      }else{
        this.packageOrderInfo()
      }
      var self = this
			window.setTimeout(function () {
				self.headParams.title = sessionStorage.titleKey
				self.headParams.description = sessionStorage.updateDescription
				self.headParams.keywords = sessionStorage.contentKey
				self.headParams.link = sessionStorage.pcfavicon
				self.$emit('updateHead')
			}, 3000)
      // this.getImageConfig();
    },
    methods: {
      getFootData() {
				this.HTTP(this.$httpConfig.aboutEtcetera, {}, "post")
					.then(res => {
						sessionStorage.setItem(
							"titleKey",
							res.data.data.intnet_title
						);
						sessionStorage.setItem("updateDescription", res.data.data.intnet_description);
						sessionStorage.setItem("contentKey", res.data.data.init_key_word);
							let title=sessionStorage.getItem('titleKey') + '-' +sessionStorage.getItem('updateDescription');
							this.showScroll.scrollTitle(title);
					})
					.catch(err => {
						console.log(err);
					});
			},
			getFavIcon() {
				this.HTTP(this.$httpConfig.getFavIcon, {}, "post")
					.then(res => {
						sessionStorage.setItem("pcfavicon", res.data.data.favicon);
					})
					.catch(err => {
						console.log(err);
					});
			},
      onChildClick (value) {
				this.fromChild = value
				if(this.fromChild == 'false') {
					location.reload();
				}
			},
      open() {
        if (!this.explain) {
          this.$message({
            showClose: true,
            message: '请填写退货理由！',
            type: 'error',
            duration: 1000
          });
          return false;
        }
        if (this.upload_list.length === 0) {
          this.$message({
            showClose: true,
            message: '请上传凭证！',
            type: 'error',
            duration: 1000
          });
          return false;
        }

        var data = {
          order_id: this.$route.params.id,
          goods_id: this.$route.params.goods_id,
          explain: this.explain,
          apply_img: this.upload_list,
          store_id: this.order_info.store_id,
          number: this.goods_num,
          price: this.goods_price,
          type:2
        }

        if(this.$route.params.type == 1){
          var api = this.$httpConfig.CommonOrderApplyForAfterSale;
        }else {
          var api = this.$httpConfig.packageOrderReturnRequest;
          data.package_id = this.returnInformation.package_id
        }
        this.HTTP(api, data , 'post', false).then(res => {
          this.$router.push({
            path: '/refund',
            query: {
              id: this.$route.params.id,
              goods_id: this.$route.params.goods_id
            }
          });
        }).catch((res) => {

        })
      },
      getImageConfig() {
        this.HTTP(this.$httpConfig.getCommentImageConfig, {
          id: this.$route.params.id
        }, 'post', false)
        .then(res => {
          this.configInfo = res.data.data;
        }).catch((res) => {
          alert(res)
        })
      },
      orderInfo() {
        this.HTTP(this.$httpConfig.generalOrderReturnDetails, {
          order_id: this.$route.params.id,
          goods_id:this.$route.params.goods_id,
        }, 'post', false).then(res => {
          this.goods = res.data.data.goods;
          this.img = res.data.data.img;
          this.store_info = res.data.data.store;
          this.order_info = res.data.data.order_goods_info;
          this.goods_num = this.order_info.goods_num;
          this.goods_price = parseFloat(this.order_info.goods_price) * parseFloat(this.order_info.goods_num);
        })
      },
      packageOrderInfo(){
        this.returnInformation=JSON.parse(sessionStorage.getItem('returnInformation'));
        this.HTTP(this.$httpConfig.packageOrderReturnDetails, {
          order_id: this.returnInformation.id,
          goods_id: this.returnInformation.goods_id,
          package_id: this.returnInformation.package_id,
        }, 'post', false).then(res => {
          this.goods = res.data.data.goods;
          this.img = res.data.data.img;
          this.store_info = res.data.data.store;
          this.order_info = res.data.data.order_goods_info;
          this.goods_num = this.order_info.package_num;
          this.goods_price = parseFloat(this.order_info.goods_discount) * parseFloat(this.order_info.package_num);
        }).catch((e)=>{
          console.log(e)
          this.$message.error(`${e.data.message}`)
        })
      },
      imgChange(e) {
        let  reader  = new  FileReader();
        let  img1 = e.target.files[0];
        let  form  =  new  FormData();
        form.append('fileData', img1, img1.name);
        // alert('hello ' + JSON.stringify(this.configInfo.token))
        form.append('imageToken', this.configInfo.token);
        form.append('sessionToken', this.configInfo.s_token);
        let config = {
          headers: {
            "Content-Type": "multipart/form-data"
          }
        };
        this.$ajax.post(this.$httpConfig.uploadImageByImageComment, form, config)
          .then(res => {
            if (res.data.status == 10001) {
              this.$router.push("/passwordLogin");
            } else {
              if (res.data.status === 1) {
                this.upload_list.push(res.data.data);
              } else {
                alert(res.data.message);
              }
            }
          });
      },
    }

  }
</script>


<style lang="less" scoped>
  .l {
    float: left;
  }

  .r {
    float: right;
  }

  .center {
    width: 1200px;
    margin: 0 auto;
    height: 100%;
  }

  .exchange {
    background: #f6f6f6;
  }

  .top {
    line-height: 42px;
    font-size: 12px;

    span {
      color: #474747;
    }

    span:last-child {
      color: #d02629;
    }
  }

  .logo {
    margin-bottom: 11px;
  }

  .middle {
    overflow: hidden;

    .left {
      width: 902px;
      height: 518px;
      background: #fff;

      .up {
        line-height: 35px;
        margin: 10px;
        border-bottom: 1px solid #e7e7e7;

        span {
          text-align: center;
          display: inline-block;
          width: 104px;
          font-size: 12px;
          color: #d02629;
          border-bottom: 2px solid #d02629;
        }
      }

      .down {
        .shouhuo {
          overflow: hidden;
          margin: 34px 0 0 42px;

          p {
            font-size: 12px;
            color: #484848;

            span {
              color: #f14343;
            }
          }

          input {
            margin: 2px 3px 0 10px;
          }
        }

        .yuanyin {
          margin: 25px 0 0 55px;
          font-size: 12px;
          color: #484848;

          span {
            color: #f14343;
            margin-right: 13px;
          }

          select {
            width: 250px;
            height: 30px;
            outline: none;
            color: #484848;
          }
        }

        .shuoming {
          overflow: hidden;
          font-size: 12px;
          color: #484848;
          margin: 10px 0 0 79px;

          textarea {
            width: 468px;
            height: 60px;
            padding: 5px 0 0 5px;
            margin-left: 11px;
          }

          span {
            margin-left: 5px;
          }
        }

        .jine {
          font-size: 12px;
          margin: 10px 0 0 57px;

          span {
            color: #f14343;
          }

          input {
            margin-left: 11px;
            width: 78px;
            height: 30px;
            border: 1px solid #ccc;
          }

          .huise {
            color: #afafaf;
          }
        }

        .pingzheng {
          margin: 19px 0 0 55px;
          font-size: 12px;
          color: #484848;

          span {
            cursor: pointer;
            display: inline-block;
            height: 28px;
            text-align: center;
            line-height: 28px;
            background: #f9f9f9;
            padding: 0 10px;
            border: 1px solid #ccc;
            margin-left: 17px;
          }
        }

        .jiahao {
          margin: 21px 0 23px 133px;
          .image-list {
            ul {
              li {
                position: relative;
                margin-right: 10px;
                width: 98px;
                height: 98px;
                -webkit-border-radius: 4px;
                -moz-border-radius: 4px;
                -ms-border-radius: 4px;
                -o-border-radius: 4px;
                border-radius: 4px;
                .close-ico {
                  position: absolute;
                  width: 24px;
                  height: 24px;
                  right: -6px;
                  top: -6px;
                  cursor: pointer;
                  z-index: 2;
                  background: url(../../assets/img/clone-icon.png) center center no-repeat;
                }
                img {
                  height: 100%;
                }
              }
            }
          }
          .upload-btn {
            width: 98px;
            border: 1px dashed #ccc;
            height: 98px;
            line-height: 116px;
            text-align: center;
            position: relative;
            -webkit-border-radius: 4px;
            -moz-border-radius: 4px;
            -ms-border-radius: 4px;
            -o-border-radius: 4px;
            border-radius: 4px;
          }
          input {
            cursor: pointer;
            opacity: 0;
            width: 100%;
            position: absolute;
            top: 0;
            left: 0;
            right: 0;
            bottom: 0;
          }
          i {
            font-size: 45px;
            color: #e4e4e4;
          }
        }

        .but {
          margin-left: 133px;

          button {
            cursor: pointer;
            width: 94px;
            height: 32px;
            border-radius: 3px;
            text-align: center;
            line-height: 32px;
            color: #fff;
            background: #d02629;
            margin-right: 18px;
          }
        }
      }
    }

    .right {
      width: 289px;
      height: 518px;
      background: #fff;

      .xiangqing {
        line-height: 34px;
        margin: 10px 12px 14px;
        border-bottom: 1px solid #e7e7e7;
      }

      .shangpin {
        overflow: hidden;
        margin: 0 12px;
        border-bottom: 1px solid #e7e7e7;

        img {
          margin: 0 11px 15px 0;
          float: left;
          width: 58px;
          height: 58px;
        }

        span {
          font-size: 12px;
          color: #333;
        }
      }

      .ul {
        margin: 9px 12px 0;

        li {
          line-height: 34px;
          font-size: 12px;
          color: #333;

          span {
            color: #ff6000;
          }

          .lanse {
            color: #59c4eb;
          }
        }
      }

      .uL {
        margin-top: 0;
      }
    }
  }

  .bottom {
    height: 489px;
    background: #fff;
    margin-bottom: 30px;

    .jieshao {
      line-height: 35px;
      margin: 10px;
      border-bottom: 1px solid #e7e7e7;

      span {
        text-align: center;
        display: inline-block;
        width: 104px;
        font-size: 12px;
        color: #d02629;
        border-bottom: 2px solid #d02629;
      }
    }

    p {
      font-size: 14px;
      color: #333;
      line-height: 73px;
      margin-left: 44px;
    }

    .spa {
      display: block;
      font-size: 14px;
      color: #5a5a5a;
      line-height: 25px;
      margin-left: 44px;
    }
  }

  .contents {
    height: 294px;

    .top {
      font-size: 15px;
      color: #494949;
    }

    p {
      font-size: 12px;
      color: #484848;
      margin-bottom: 20px;
    }

    input {
      margin-right: 11px;
    }

    button {
      width: 72px;
      height: 32px;
      text-align: center;
      line-height: 32px;
      border-radius: 3px;
      margin-right: 18px;
      cursor: pointer;
    }

    button:nth-of-type(1) {
      background: #ff6000;
      color: #fff;
    }

    button:nth-of-type(2) {
      background: #fafafa;
      border: 1px solid #e7e6e6;
    }
  }
</style>
