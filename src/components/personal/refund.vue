<template>
  <div class="exchange">
    <!-- <common-header></common-header> -->
    <common-header v-on:childToParent="onChildClick"></common-header>
    <my-header></my-header>
    <div class="center">
      <div class="top"><span>您的位置： </span> <span>交易中心</span> > <span>我的订单 </span> > <span>申请售后</span> > <span>仅退款</span>
      </div>
      <img v-show="logo1" class="logo" src="../../assets/img/jintuikuan1.png"/><img v-show="logo2" class="logo"
                                                                                    src="../../assets/img/jintuikuan2.png"/><img
      v-show="logo3" class="logo" src="../../assets/img/jintuikuan3.png"/>
      <div class="middle">
        <div class="left l" v-show="logo1">
          <div class="up"><span>仅退款</span></div>
          <div class="down">
            <div class="shouhuo">
              <p class="l">是否收到货：<span>*</span></p>
              <p class="l"><input class="l" type="radio" name="is"/>未收到货</p>
              <p class="l"><input class="l" type="radio" name="is"/>已收到货</p>
            </div>
            <div class="yuanyin">退款原因：<span>*</span><select name="">
              <option value="">请选择仅退款原因</option>
              <option value="">大小尺寸与商品描述不相符</option>
              <option value="">大小尺寸与商品描述不相符</option>
            </select></div>
            <div class="shuoming">
              <p class="l">说明：</p><textarea class="l"></textarea><span>(2/200字)</span></div>
            <div class="jine">退款金额：<span>*</span><input type="text"/>
              元（最多<span>288.68</span>元，含发货邮费<span>0.00</span>元）<span class="huise">退款金额说明</span></div>
            <div class="pingzheng">上传凭证：<span>选择凭证图片</span></div>
            <div class="jiahao"><i class="el-icon-plus"></i></div>
            <div class="but">
              <button @click="open">提交申请</button>
            </div>
          </div>
        </div>
        <div class="left l Logo2" v-show="logo2">
          <div class="Box">
            <div class="chuli">请等待卖家处理</div>
            <p class="same">. 如果卖家同意或者在 <span>04</span>天<span>23</span>时<span>59</span>分<span>48</span>秒内未处理，系统将退款给您。
            </p>
            <p class="same">. 如果卖家拒绝，您可以修改退款申请后再次发起，卖家会重新处理</p>
          </div>
          <p class="xiugai">您好可以：<a @click="xiugai">修改退款申请</a><a @click="hit">取消退款申请</a></p>

        </div>
        <div class="left l logo3" v-show="logo3">
          <div>退款成功</div>
          <p class="same">. 售后达成时间 2017-06-20-10：50</p>
          <p class="same">. 退款金额：<span>230.00</span>元</p>
        </div>
        <div class="right r">
          <div class="xiangqing" v-text="title"></div>
          <div class="shangpin"><img :src="URL + goods.pic_url"/><span>{{goods.title}}</span>
          </div>
          <ul class="ul">
            <li>卖 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 家：{{store_info.shop_name}}</li>
            <li>联系电话：{{store_info.mobile}}</li>
            <li>订单编号：<span class="lanse">{{order_info.order_sn_id}}</span></li>
            <li>单 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 价：<span>{{goods.price_member}}</span>元*{{goods.goods_num}}（数量）</li>
            <li>邮 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 费：<span>{{order_info.shipping_monery}}</span>元</li>
          </ul>
          <ul class="ul uL" v-show="ul">
            <li>退款编号：827790197963683</li>
            <li>退款金额：<span>668.68</span>元</li>
            <li>原 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 因：大小尺寸与商品描述不相符</li>
            <li>要 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 求：仅退款</li>
            <li>货物状态：已收到货</li>
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
    <modal title="撤销申请" :width="625" :is-show="isopen" transition="fadeDown" @close="isopen=false" :show-footer="false">
      <div class="contents">
        <p class="top">目前售后申请仅有一次机会。为确保您的利益，请您告知撤销申请是由于：</p>
        <p><input type="radio" name="if"/>卖家要求先撤销，然后才肯提供售后服务</p>
        <p><input type="radio" name="if"/>和卖家协商一致。已解决售后问题</p>
        <p><input type="radio" name="if"/>其他</p>
        <button>放弃撤销</button>
        <button>确认撤销</button>

      </div>
    </modal>
    <foot-com></foot-com>
  </div>
</template>

<script>
  import myHeader from './header/myHeader.vue' //个人中心的头部
  export default {
    data() {
      return {
        logo1: false,
        logo2: true,
        logo3: false,
        isopen: '',
        title: '订单详情',
        ul: false,
        order_info: {},
        store_info: {},
        goods: {},
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
	created() {
		this.getFootData();
		this.getFavIcon(); 
	},
    mounted() {
      this.orderInfo();
        var self = this
            window.setTimeout(function () {
                self.headParams.title = sessionStorage.titleKey
                self.headParams.description = sessionStorage.updateDescription
                self.headParams.keywords = sessionStorage.contentKey
                self.headParams.link = sessionStorage.pcfavicon
                self.$emit('updateHead')
        }, 3000)
    },
    components: {
      myHeader
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
      orderInfo() {
        this.HTTP(this.$httpConfig.orderDetails, {
          id: this.$route.query.id
        }, 'post', false).then(res => {
          for (let index = 0; index < res.data.data.goods.length; index++) {
            if (res.data.data.goods[index].goods_id === this.$route.query.goods_id) {
              this.goods = res.data.data.goods[index];
            }
          }
          this.store_info = res.data.data.store;
          this.order_info = res.data.data.order;
        })
      },
      open() {
        this.logo1 = false
        this.logo2 = true
        this.title = '仅退款'
        this.ul = true
      },
      xiugai() {
        this.logo1 = true
        this.logo2 = false
      },
      hit() {
        this.isopen = !this.isopen
      }
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
            width: 121px;
            height: 28px;
            text-align: center;
            line-height: 28px;
            background: #f9f9f9;
            border: 1px solid #ccc;
            margin-left: 17px;
          }
        }

        .jiahao {
          width: 98px;
          height: 98px;
          border: 1px dashed #ccc;
          margin: 21px 0 23px 133px;
          line-height: 116px;
          text-align: center;

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

    .logo2 {
      .Box {
        margin: 0 15px;
        height: 203px;
        border-bottom: 1px solid #e7e7e7;

        .chuli {
          font-size: 14px;
          margin: 46px 0 22px 43px;
          line-height: 18px;

          p {
            font-size: 18px;
            margin-right: 42px;
          }

          span {
            color: #d02629;
          }
        }

        .same {
          font-size: 14px;
          line-height: 26px;
          width: 723px;
          margin-left: 44px;
        }
      }

      p.xiugai {
        margin: 42px 0 0 63px;
        font-size: 14px;

        a {
          padding-right: 29px;
          font-size: 14px;
        }
      }
    }

    .Logo2 {
      .Box {
        margin: 0 15px;
        height: 132px;
        border-bottom: 1px solid #e7e7e7;

        .chuli {
          font-size: 18px;
          margin: 46px 0 22px 43px;
          line-height: 18px;
        }

        .same {
          font-size: 14px;
          line-height: 26px;
          width: 723px;
          margin-left: 44px;

          span {
            color: #d02629;
          }
        }
      }

      p.xiugai {
        margin: 42px 0 0 63px;
        font-size: 14px;

        a {
          padding-right: 29px;
          font-size: 14px;
        }
      }
    }

    .logo3 {
      div {
        font-size: 17px;
        margin: 46px 0 20px 44px;
      }

      .same {
        font-size: 14px;
        margin-left: 44px;
        line-height: 26px;

        span {
          color: #d02629;
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
