<template>
  <div class="myOrder l">
    <div class="dingDan">
      <ul class="top">
        <li>订单详情</li>
      </ul>
      <div class="zhuangtai">
        <p>当前订单状态：{{$Status[goods[0].status]}}</p>
      <div class="service-reset">
          . 如果没有收到货，或收到货后出现问题，您可以联系卖家协商解决
          <div id="dom" @click="openkefu(order)">
            <img src="@/assets/img/people_ser.png" />
            <span>客服</span>
          </div>
        </div>
        <p>. 如果卖家没有履行应尽的承诺，您可以“投诉维权”</p>
      </div>
      <div class="maijia">
        <p>买家信息</p>
        <p>收货地址：{{address.realname}}，{{address.mobile}}, {{address.provName + address.cityName + address.distName + address.address}} {{address.zipcode === null ? '暂无邮政编码信息' : address.zipcode}}</p>
        <p>运送方式快递</p>
      </div>
      <div class="Maijai">
        <p>卖家信息</p>
        <ul>
          <li class="l">店铺名称：{{store.shop_name}} 
                <div class="wrapper-reset" @click="openkefu(order)">
              <img src="@/assets/img/people_ser.png" />
              <span>客服</span>
            </div>
              </li>
          <li class="l">真实姓名：{{store.person_name}}</li>
          <li class="l">城市：{{store.store_address}}</li>
          <li class="l">联系电话：{{store.mobile}}</li>
          <li class="l">邮件：</li>
          <li class="l">支付宝：{{store.alipay_account}}</li>
        </ul>
      </div>
      <div class="Maijai">
        <p>订单信息</p>
        <ul>
          <li class="l">订单编号:{{order.id}}</li>
          <li class="l">支付宝交易号：{{order.order_sn_id}}</li>
          <li class="l">创建时间：{{~~order.create_time | formatDate}}</li>
          <li class="l" v-if="order.over_time">发货时间：{{~~order.over_time | formatDate}}</li>
          <li class="l" v-if="order.pay_time">付款时间：{{~~order.pay_time | formatDate}}</li>
          <li class="l" v-if="order.delivery_time">交货时间：{{~~order.delivery_time | formatDate}}</li>
        </ul>
      </div>
      <div class="shangpin">
        <div class="up">
          <p>宝贝</p>
          <p>宝贝属性</p>
          <p>状态</p>
          <p>单价</p>
          <p>数量</p>
          <p>优惠</p>
          <p>商品总价</p>
        </div>
        <div class="down" v-for="(goods, index) in goods" :key="index">
          <p><img v-lazy="URL + '/' + goods.pic_url" /><span>{{goods.title}}</span></p>
            <p>
              <span>无</span>
            </p>
            <p>{{$Status[goods.status]}}</p>
            <p>￥{{goods.goods_discount}}</p>
            <p>{{goods.package_num}}</p>
            <p>无</p>
            <p><span v-if="index === 0">￥{{parseFloat(order.price_sum) + parseFloat(order.shipping_monery)|keep2Num}}</span><span v-if="index === 0">（含运费：￥{{order.shipping_monery|keep2Num}}）</span></p>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
  export default {
    data() {
      return {
        store: [],
        goods: [],
        address: [],
        order: [],
      };
    },
    created() {
      this.HTTP(this.$httpConfig.package.orderDetail, {
          id: this.$route.query.id
        }, 'post', false).then(res => {
          this.store = res.data.data.store
          this.goods = res.data.data.goods
          this.order = res.data.data.order
          this.address = res.data.data.address
        }).catch((e)=>{
          this.$message(`${e.data.message}`)
        })
    },
    methods: {
            openkefu(item) {
      this.HTTP(this.$httpConfig.serviceList, {
        store_id: item.store_id
      })
        .then(res => {
          if (res.data.status == 10001) {
            this.$router.push("/passwordLogin");
             return;
          }
          window.open(res.data.data);
        })
        .catch(err => {
          console.log(err);
        });
    },
    }
  };
</script>
<style lang="less" scoped>
.wrapper-reset {
  cursor: pointer;
  display: inline-block;
  margin-left: 20px;
  background: #fbfbf1;
  span {
    display: inline-block;
    margin-left: 8px;
    margin-right: 8px;
  }
  img {
    margin-left: 8px;
    margin-top: -5px;
  }
}
.service-reset {
  display: flex;
  margin-left: 24px;

  #dom {
    cursor: pointer;
    margin-left: 30px;
    display: flex;
    align-items: center;
    border: 1px solid #eeeeee;
    background: #fbfbf1;
    width: 8%;
    span {
      margin-left: 10px;
      margin-top: 2px;
    }
    img {
      margin-left: 10px;
    }
  }
}
</style>
<style lang="less" scoped>
  .l {
    float: left;
  }
 
  .dingDan {
    width: 980px;
    background: #fff;
    margin: 16px 0;
    .top {
      height: 37px;
      border-bottom: 1px solid #e7e7e7;
      margin: 14px 17px 0;
      li {
        width: 104px;
        text-align: center;
        line-height: 35px;
        border-bottom: 2px solid #d02629;
        color: #d02629;
        font-size: 14px;
      }
    }
    .zhuangtai {
      height: 127px;
      border: 1px solid #bce8f1;
      background: #eff8ff;
      margin: 18px 17px 11px;
      p {
        font-size: 12px;
        color: #999;
        line-height: 24px;
        margin-left: 23px;
      }
      p:nth-of-type(1) {
        font-size: 15px;
        color: #333;
        margin: 25px 0 9px 24px;
      }
    }
    .maijia {
      margin: 0 17px;
      p:nth-of-type(1) {
        line-height: 30px;
        border-bottom: 1px solid #e7e7e7;
        font-size: 14px;
        color: #333;
        margin-bottom: 11px;
      }
      p {
        font-size: 12px;
        color: #333;
        padding-left: 24px;
        line-height: 24px;
      }
    }
    .Maijai {
      margin: 14px 17px;
      p {
        padding-left: 24px;
        line-height: 30px;
        border-bottom: 1px solid #e7e7e7;
        font-size: 14px;
        color: #333;
        margin-bottom: 11px;
      }
      li {
        line-height: 39px;
        font-size: 12px;
        color: #333;
      }
      ul {
        overflow: hidden;
        margin-left: 23px;
        li:nth-of-type(1) {
          width: 329px;
        }
        li:nth-of-type(4) {
          width: 329px;
        }
        li:nth-of-type(2) {
          width: 356px;
        }
        li:nth-of-type(5) {
          width: 356px;
        }
        li:nth-of-type(3) {
          width: 236px;
        }
        li:nth-of-type(6) {
          width: 236px;
        }
      }
    }
    .shangpin {
      margin: 0 auto;
      border: 1px solid #e7e6e6;
      overflow: hidden;
      width: 896px;
      .up {
        overflow: hidden;
        background: #f1f1f1;
        line-height: 42px;
        background: #f1f1f1;
      }
      p {
        float: left;
        font-size: 12px;
        color: #333;
        text-align: center;
      }
      p:nth-of-type(1) {
        width: 263px;
      }
      p:nth-of-type(2) {
        width: 119px;
      }
      p:nth-of-type(3) {
        width: 114px;
      }
      p:nth-of-type(4) {
        width: 89px;
      }
      p:nth-of-type(5) {
        width: 79px;
      }
      p:nth-of-type(6) {
        width: 99px;
      }
      p:nth-of-type(7) {
        width: 127px;
        border-right: 0 !important;
      }
      .down {
        overflow: hidden;
        height: 109px;
        img {
          float: left;
          margin: 0 11px 0 13px;
          width: 70px;
        }
        p {
          padding-top: 19px;
          height: 100%;
          border-right: 1px solid #e7e6e6;
          span {
            display: block;
          }
        }
        p:nth-of-type(1) {
          text-align: left;
          span {
            width: 149px;
            display: inline-block;
          }
        }
      }
    }
  }
</style>