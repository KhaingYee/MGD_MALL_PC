<template>
  <div class="all-order">
    <div class="filter clearfix">
      <p class="search l">
        <el-input
          placeholder="输入商品标题或订单号进行搜索"
          v-model="keyWords"
          size="mini"
          class="input-with-select"
        >
          <el-button slot="append" @click="search(1)">订单搜索</el-button>
        </el-input>
      </p>
      <p @click="filter" class="tiaojian l">
        <span>精简筛选条件</span>
        <i :class="filterShow ? 'el-icon-arrow-up' : 'el-icon-arrow-down'"></i>
      </p>
      <div v-show="filterShow" class="l ipn">
        <p class="l leixing">
          订单类型
          <el-select
            size="mini"
            class="sel"
            v-model="order_status"
            placeholder="全部"
          >
            <el-option
              v-for="item in orderStatus"
              :key="item.status"
              :label="item.message"
              :value="item.status"
            >
            </el-option>
          </el-select>
        </p>
        <p class="l data">
          成交时间
          <el-date-picker
            size="mini"
            value-format="yyyy-MM-dd HH:mm:ss"
            v-model="start_time"
            type="datetime"
            placeholder="请选择时间范围起始"
          ></el-date-picker>
          -
          <el-date-picker
            size="mini"
            value-format="yyyy-MM-dd HH:mm:ss"
            v-model="end_time"
            type="datetime"
            placeholder="请选择时间范围结束"
          ></el-date-picker>
        </p>
      </div>
      <div v-show="filterShow" class="l ipn">
        <p class="l leixing">
          评价状态
          <el-select size="mini" v-model="comment_status" placeholder="全部">
            <el-option
              v-for="item in evaluateStatus"
              :key="item.status"
              :label="item.message"
              :value="item.status"
            >
            </el-option>
          </el-select>
        </p>
      </div>
    </div>
    <div class="thead">
      <p class="l">宝贝</p>
      <p class="l">单价</p>
      <p class="l">数量</p>
      <p class="l">商品操作</p>
      <p class="l">实付款</p>
      <p class="l">交易状态<i class="el-icon-caret-bottom"></i></p>
      <p class="l">交易操作</p>
    </div>
    <div class="fullOrde">
      <div class="alike" v-for="(item, index) in data.list" :key="index">
        <div class="both">
          <input class="l" type="checkbox" />
          <p class="l">{{ item.create_time }}</p>
          <p class="l">订单号： {{ item.order_sn_id }}</p>
          <p class="l">
            店铺：
            <router-link
              target="_blank"
              :to="{ path: '/storehome', query: { id: item.store_id } }"
              >{{ item.store.shop_name }}
            </router-link>
          </p>
            <el-dropdown v-if="item.store.store_grade_name">
              <span class="first-name">{{ item.store.store_grade_name }}</span>
              <el-dropdown-menu slot="dropdown" v-if="item.store.classification">
              <el-dropdown-item>{{ item.store.classification }}</el-dropdown-item>
              </el-dropdown-menu>
            </el-dropdown>
            <img src="@/assets/img/people_ser.png" class="dom-img" @click="openkefu(item)"/>
          <!-- <div class="service-reset">
            <div id="dom" @click="openkefu(item)">
              <span>客服</span>
              <img src="@/assets/img/people_ser.png" />
            </div>
          </div> -->

          <i
            @click="deleteOrder(item.id)"
            v-if="item.order_status == 4 || item.order_status == '-1'"
            class="el-icon-delete r"
          ></i>
        </div>
        <div class="order-item clearfix">
          <div class="order-info l">
            <div class="zuo" v-for="(goods, i) in item.goods" :key="i">
              <div class="huowu">
                <router-link
                  target="_blank"
                  class="a-block"
                  :to="{ path: '/shopsn_product', query: {  id: item.id,
                        goods_id: goods.goods_id, } }"
                >
                  <img :src="URL + goods.pic_url" />
                </router-link>
                <p>
                  <router-link
                    target="_blank"
                    class="a-block"
                    :to="{
                      path: '/shopsn_product',
                      query: { id: goods.goods_id }
                    }"
                  >
                    {{ goods.title }}
                    <span v-if="goods.cut != 0" style="color:red"
                      >(满{{ goods.expense }} 减{{ goods.cut }})</span
                    >
                  </router-link>
                </p>
                <!-- <p v-if='goods.cut'>满{{goods.cut}}减{{goods.expense}}</p> -->
                <p v-if="goods.goods_price != '0.00'">
                  ￥{{ goods.goods_price }}
                </p>

                <p v-else style="color: #d02629;">赠品</p>
                <p>{{ goods.goods_num }}</p>
                <div class="l g-operation">
                  <!-- to="complain" -->
                  <router-link
                    class="goods-operate"
                    target="_blank"
                    :to="{ path: '/complain', query: { id: item.id,
                        goods_id: goods.goods_id,
                        store_id: item.store_id } }"
                    >投诉卖家</router-link
                  >
                  <router-link
                    v-if="goods.status == 1 || goods.status == 2"
                    v-show="checkOverTime(item.over_time)"
                    class="goods-operate"
                    target="_blank"
                    :to="{
                      name: 'apply',
                      params: {
                        id: item.id,
                        goods_id: goods.goods_id,
                        order_sn_id: item.order_sn_id
                      }
                    }"
                    >退款/退换货
                  </router-link>
                  <router-link
                    v-if="
                      goods.status == 4 &&
                        goods.status != 5 &&
                        goods.status != 6 &&
                        goods.status != 7 &&
                        goods.status != 8 &&
                        goods.status != 9 &&
                        checkOverTime(item.over_time)
                    "
                    class="goods-operate"
                    target="_blank"
                    :to="{
                      name: 'apply',
                      params: {
                        id: item.id,
                        goods_id: goods.goods_id,
                        order_sn_id: item.order_sn_id
                      }
                    }"
                    >售后处理
                  </router-link>
                  <span
                    v-if="
                      goods.status == 5 ||
                        goods.status == 6 ||
                        goods.status == 7 ||
                        goods.status == 8 ||
                        goods.status == 9 ||
                        goods.status == 10 ||
                        goods.status == 11
                    "
                    class="goods-operate"
                    >{{ status(goods.status) }}</span
                  >
                </div>
              </div>
            </div>
          </div>
          <div
            class="right r"
            :style="{ height: item.goods.length * 110 + 'px' }"
          >
            <p>
                <span>￥{{item.price_sum}}</span>
                <span>{{item.message}}</span>
                <!-- <span v-if='add.full'>满{{add.full}}减{{add.expression}}</span> -->
            </p>
            <p>
              <span v-if="item.order_status == 0">等待买家付款</span>
              <span v-if="item.order_status == 1">等待卖家发货</span>
              <router-link
                target="_blank"
                class="a-block"
                :to="{ path: '/orderDetail', query: { id: item.id } }"
                >订单详情
              </router-link>
              <a
                @click="lookLogistics(item)"
                class="a-block"
                v-if="
                  ~~item.order_status === 2 ||
                    ~~item.order_status === 3 ||
                    ~~item.order_status === 4
                "
                >查看物流</a
              >
              <!--<a @click="confirmReceipt(item)" class="a-block" v-if="~~item.order_status === 3">确认收货</a>-->
            </p>
            <p v-if="~~item.order_status !== 4" class="btns">
              <span
                ref="msg"
                v-if="~~item.order_status != 3 && ~~item.order_status != 4"
                :class="~~item.order_status === 0 ? 'quxiao' : ''"
                @click="state(item)"
                >{{ Status(item.order_status) }}</span
              >
              <span
                ref="msg"
                v-if="~~item.order_status === 4 && ~~item.comment_status === 1"
                >已评价</span
              >
              <a
                v-if="item.order_status == 0"
                class="a-block"
                @click="cancelOrder(item.id)"
                >取消订单</a
              >
              <span
                ref="msg"
                class="receiving-goods"
                @click="confirmReceipt(item)"
                v-if="~~item.order_status === 3"
                >确认收货</span
              >
            </p>
            <p v-else class="evaluate-btn">
              <span class="appraise" v-for="(good, g) in item.goods" :key="g">
                <span class="already-evaluated" v-if="good.comment == 1">
                  已评价
                </span>
                <span
                  v-else
                  @click="
                    $router.push({
                      path: '/publishComment',
                      query: { id: item.id, goods_id: good.goods_id, type: 0 }
                    })
                  "
                  >评价</span
                >
              </span>
            </p>
          </div>
        </div>
      </div>
      <div class="pagation" v-if="data.length != 0">
        <el-pagination
          v-if="flag === true"
          background
          layout="prev, pager, next"
          :current-page.sync="currentPage"
          :total="~~data.count"
          :page-size="data.page_size"
          @current-change="handleCurrentChange"
        >
        </el-pagination>
        <el-pagination
          v-if="flag === false"
          background
          layout="prev, pager, next"
          :current-page.sync="currentPage"
          :total="~~data.count"
          :page-size="data.page_size"
          @current-change="handleCurrentChange1"
        >
        </el-pagination>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      data: [],
      
      filterShow: false,
      currentPage: 1,
      message: "",
      keyWords: "",
      order_status: "",
      comment_status: "",
      start_time: "",
      end_time: "",
      flag: true,
      evaluateStatus: [
        {
          message: "全部",
          status: ""
        },
        {
          message: "未评价",
          status: 0
        },
        {
          message: "已评价",
          status: 1
        }
      ],
      orderStatus: [
        {
          message: "全部",
          status: ""
        },
        {
          message: "取消订单",
          status: -1
        },
        {
          message: "未支付",
          status: 0
        },
        {
          message: "已支付",
          status: 1
        },
        {
          message: "发货中",
          status: 2
        },
        {
          message: "已发货",
          status: 3
        },
        {
          message: "已收货",
          status: 4
        },
        {
          message: "退货审核中",
          status: 5
        },
        {
          message: "审核失败",
          status: 6
        },
        {
          message: "审核成功",
          status: 7
        },
        {
          message: "退款中",
          status: 8
        },
        {
          message: "退款成功",
          status: 9
        }
      ],
      storeId: '',
      goodId: '',
      deliveryMoney: '',
      freight_price: '',
      // provId: '',
      // cityId: '',
      // distId: ''
    };
  },
  created() {
    var timestamp = Date.parse(new Date());
    this.Order();
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
    // 确认收货
    confirmReceipt(item) {
      this.$confirm("确认收货", "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning",
        closeOnClickModal: false,
        lockScroll: false,
        center: true
      })
        .then(() => {
          this.HTTP(
            this.$httpConfig.OrdinaryGoods,
            {
              id: item.id
            },
            "post",
            false
          )
            .then(res => {
              let that = this;
              setTimeout(that.Order(), 1000);
            })
            .catch(e => {
              console.log(e);
            });
        })
        .catch(() => {});
    },
    //判断是否售后七天
    checkOverTime(time) {
      if (!time) {
        return true;
      }
      var timestamp = Date.parse(new Date()) / 1000;
      var overTime = Number(time) + 86400 * 7;
      if (timestamp > overTime) {
        return false;
      } else {
        return true;
      }
    },
    //state(parseFloat(item.price_sum) + parseFloat(item.shipping_monery), item.id, item.goods[0].goods_id, item.order_status)
    // state (price, id, goodsId, status) {
    //   switch (~~status) {
    //     case 0:
    //         this.$router.push({
    //           name: "confirmOrder",
    //           params: {
    //             goods_id: goodsId,
    //             num: price
    //           }
    //         });
    //       break;

    //     default:
    //       break;
    //   }
    // },
    //跳转至订单详情
    //跳转至订单详情
    state(item) {
      this.HTTP(this.$httpConfig.regularOrders, { id: item.id }, "post")
        .then(res => {
          this.$message.success(res.data.message);
          if (res.data.status === 1) {
            // let total_price = parseFloat(item.price_sum) + parseFloat(item.shipping_monery);
            // if(this.deliveryMoney == -1) {
            //   var total_price = parseFloat(item.price_sum);
            // }
            // else if(this.deliveryMoney) {
            //   var total_price = parseFloat(item.price_sum) + parseFloat(this.deliveryMoney);
            // }
            // else if(this.freight_price == 0) {
            //   var total_price = parseFloat(item.price_sum);
            // }
            // else {
            //   var total_price = parseFloat(item.price_sum) + parseFloat(this.freight_price);
            // }
            var total_price = parseFloat(item.price_sum);
            total_price = total_price.toFixed(2);
            this.$router.push({
              path: "/pay",
              query: {
                total_price: total_price,
                state: "order"
              }
            });
          }
        })
        .catch(e => {
          this.$message.success(e.data.message);
        });
    },
    filter() {
      this.filterShow = !this.filterShow;
    },
    cancelOrder(id) {
      //取消订单
      this.$confirm("您确定要取消该订单吗?", "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning",
        lockScroll: false,
        center: true,
        closeOnClickModal: false
      })
        .then(() => {
          this.HTTP(
            this.$httpConfig.cancelOrder,
            {
              id: id
            },
            "post"
          ).then(res => {
            if (this.flag) {
              this.Order();
            } else {
              this.search(1);
            }
          });
        })
        .catch(() => {});
    },
    deleteOrder(id) {
      //删除订单
      this.$confirm("您确定要删除该订单吗?", "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning",
        lockScroll: false,
        center: true,
        closeOnClickModal: false
      })
        .then(() => {
          this.HTTP(
            this.$httpConfig.delOrder,
            {
              id: id
            },
            "post"
          ).then(res => {
            if (this.flag) {
              this.Order();
            } else {
              this.search(1);
            }
          });
        })
        .catch(() => {});
    },
    search(v) {
      if (v === 1) {
        this.currentPage = 1;
      }
      this.flag = false;
      this.HTTP(
        this.$httpConfig.orderSearch,
        {
          page: this.currentPage,
          keyWords: this.keyWords,
          order_status: this.order_status,
          comment_status: this.comment_status,
          start_time: this.start_time,
          end_time: this.end_time
        },
        "post",
        false
      )
        .then(res => {
          this.data = res.data.data;
        })
        .catch(() => {
          this.data = [];
        });
    },
    handleCurrentChange(val) {
      this.currentPage = val;
      this.Order();
    },
    handleCurrentChange1(val) {
      this.currentPage = val;
      this.search();
    },
    prevClick(val) {
    },

    /**
     * 查看物流
     */
    lookLogistics(item) {
      window.open(
        window.location.origin +
          "/logistics?id=" +
          item.id +
          "&express_id=" +
          item.express_id +
          "&exp_id=" +
          item.exp_id +
          "&status=" +
          item.order_status +
          "&type=0"
      );
    },
    Order() {
      this.HTTP(
        this.$httpConfig.fullOrder,
        {
          page: this.currentPage
        },
        "post",
        false
      )
        .then(res => {
          this.data = res.data.data;
          // this.provId = this.data.UserAddress.prov.id;
          // this.cityId = this.data.UserAddress.city.id;
          // this.distId = this.data.UserAddress.dist.id;
          for(let a in this.data.list) {
            this.goodId = this.data.list[a].id;
            this.storeId = this.data.list[a].store_id;
          }
          // this.getDistributionFee();
          // this.getFreight();
        })
        .catch(res => {
          this.data = [];
        });
    },
    // getDistributionFee() { //获取配送端
    //   this.HTTP(this.$httpConfig.deliveryCalcus, {
    //     store_id: this.storeId,
    //     goods_id: this.goodId,
    //     prov_id: this.provId,
    //     city_id: this.cityId,
    //     dist_id: this.distId
    //     }, 'post').then((res) => {
    //       if (res.data.status == 1) {
    //         this.deliveryMoney = res.data.data.money;
    //       }
    //     })
    //   },
    //   getFreight() {
    //     this.HTTP(this.$httpConfig.freightCalcus, {
    //       store_id: this.storeId,
    //       goods_id: this.goodId,
    //       prov_id: this.provId,
    //       city_id: this.cityId,
    //       dist_id: this.distId
    //     }, 'post').then((res) => {
    //       this.freight_price = res.data.data;
    //     }).catch((res) => {
    //       console.log(res);
    //     })
    //   },
    Status(status) {
      switch (~~status) {
        case -1:
          return "取消订单";
        case 0:
          return "立即付款";
        case 1:
          return "已支付";
        case 2:
          return "发货中";
        case 3:
          return "已发货";
        case 4:
          return "已收货";
        case 5:
          return "退货审核中";
        case 6:
          return "审核失败";
        case 7:
          return "审核成功";
        case 8:
          return "退款中";
        case 9:
          return "退款成功";
        default:
          return "";
      }
    },
    status(status) {
      switch (~~status) {
        case -1:
          return "取消订单";
        case 0:
          return "未支付";
        case 1:
          return "已支付";
        case 2:
          return "发货中";
        case 3:
          return "已发货";
        case 4:
          return "已收货";
        case 5:
          return "退货审核中";
        case 6:
          return "审核失败";
        case 7:
          return "审核成功";
        case 8:
          return "退款中";
        case 9:
          return "退款成功";
        case 10:
          return "退货中";
        case 11:
          return "卖家已收货";
        default:
          return "";
      }
    }
  }
};
</script>
<style>
.el-popper[x-placement^=bottom] .popper__arrow {
  border-bottom-color: red !important;
}
.el-popper .popper__arrow {
  border-width: 6px;
  filter: drop-shadow(0 2px 12px rgba(0,0,0,.03));
  
}</style>
<style lang="less" scoped>
 .el-dropdown{
margin: 0 0 0 10px;
}
.el-dropdown-menu {
    position: absolute !important;
    margin: 0 !important;
    background-color: #FFF;
    border: 1px solid #de2d35;
    border-radius: 4px;
    -webkit-box-shadow: 0 2px 12px 0 rgba(0,0,0,.1);
    box-shadow: 0 2px 12px 0 rgba(0,0,0,.1);
}
.el-dropdown-menu__item {
  line-height: 8px !important;
  font-size: 12px !important;
  color:rgb(101, 101, 101) !important;
}
  .el-dropdown-menu__item:focus, .el-dropdown-menu__item:not(.is-disabled):hover{
    color:rgb(101, 101, 101) !important;
    background-color: #ffffff !important;
  }

.el-popper .popper__arrow {
  border-width: 6px;
  filter: drop-shadow(0 2px 12px rgba(0,0,0,.03));   
}

.el-popper[x-placement^=bottom] .popper__arrow {
  border-bottom-color: red !important;
}
.el-popper .popper__arrow {
  border-width: 6px;
  filter: drop-shadow(0 2px 12px rgba(0,0,0,.03));
  
}
.el-popper .popper__arrow, .el-popper .popper__arrow:after {
  position: absolute;
  display: block;
  width: 0;
  height: 0;
  border-color: transparent;
  border-style: solid;  
} 
</style>
<style lang="less" scoped>
.service-reset {
  // position: relative;
  #dom {
    /*right: 550px;*/
    right: 483px;
    cursor: pointer;
    line-height: 26px;
    margin-top: 7px;
    position: absolute;
    align-items: end;
    border: 1px solid #eeeeee;
    background: #fbfbf1;
    span {
      margin-left: 10px;
      margin-right: 10px;
      font-size: 10px;
    }
  }
}
</style>
<style lang="less" scoped>
.el-date-editor.el-input {
  width: 178px;
}

.l {
  float: left;
}

.r {
  float: right;
}

.pagation {
  display: flex;
  justify-content: center;
}

.center {
  width: 1200px;
  margin: 0 auto;
  height: 100%;
}

.el-input__inner {
  color: red !important;
}

.all-order {
  .filter {
    .search {
      margin: 23px 12px 15px 17px;
      width: 314px;
    }

    .tiaojian {
      font-size: 12px;
      color: #333;
      margin-top: 28px;
      cursor: pointer;

      .delete-order {
        cursor: pointer;
      }
    }

    .ipn {
      display: block;
      width: 90%;
      margin: 0 0 14px 17px;

      .leixing {
        font-size: 12px;
        color: #333;

        select {
          width: 148px;
          height: 26px;
          border-color: #ccc;
          color: #a0a0a0;
          outline: none;
        }
      }

      .data {
        margin-left: 115px;
        font-size: 12px;
        color: #333;

        input {
          height: 26px;
          padding-left: 20px;
          border: 1px solid #bfbfbf;
        }
      }
    }
  }

  .thead {
    overflow: hidden;
    height: 38px;
    background: #f5f5f5;
    border: 1px solid #e7e6e6;
    margin: 0 17px 20px 17px;
    width: 946px;
    line-height: 38px;

    p {
      font-size: 12px;
      color: #474747;

      i {
        color: #999;
        margin-left: 9px;
      }
    }

    p:nth-of-type(1) {
      margin: 0 205px 0 134px;
    }

    p:nth-of-type(3) {
      margin: 0 70px 0 50px;
    }

    p:nth-of-type(5) {
      margin: 0 65px 0 74px;
    }

    p:nth-of-type(7) {
      margin-left: 51px;
    }
  }

  .alike {
    overflow: hidden;
    border: 1px solid #e7e6e6;
    margin: 0 17px 10px;

    .a-block {
      display: block;
    }

    a {
      font-size: 12px;
      color: #333;
    }

    a:hover {
      color: red;
    }

    .both {
      height: 42px;
      line-height: 42px;
      background: #f1f1f1;

      i {
        cursor: pointer;
        font-size: 16px;
        margin: 13px 16px 0 0;
        color: #999;
        font-weight: 900;
      }

      input {
        margin: 16px 14px 0 13px;
      }

      p {
        font-size: 12px;
        color: #333;
      }

      p:nth-of-type(2) {
        margin: 0 67px 0 25px;
      }

      // img {
      //   float: left;
      //   margin: 4px 0px 0px 10px;
      // }
      .first-name{
        color: #ffffff;
        background-color: #de2d35;
        font-size: 12px;
        border-radius: 3px;
        padding: .8px 7px 1.3px 7px;
      }
      .dom-img{
        margin: 0 0 6px 10px;
      }
    }

    .zuo {
      overflow: hidden;
      // width: 613px;

      .huowu {
        height: 110px;
        display: flex;
        align-items: center;

        .g-operation {
          text-align: center;

          span {
            font-size: 12px;
          }

          .goods-operate {
            display: block;
            margin-top: 5px;
          }
        }

        img {
          width: 70px;
          height: 70px;
          float: left;
          margin: 0 10px 0 13px;
        }

        p {
          float: left;
          font-size: 12px;
          color: #333;
        }

        p:nth-of-type(1) {
          width: 147px;
          margin: 0 117px 0 0;
        }

        p:nth-of-type(3) {
          margin: 0 83px 0 44px;
        }
      }
    }

    .right {
      height: 110px;
      overflow: hidden;

      p {
        float: left;
        border-left: 1px solid #e7e6e6;
        height: 100%;
        display: flex;
        align-items: center;
        flex-direction: column;
        justify-content: center;

        span {
          display: block;
          font-size: 12px;
          color: #333;
        }
      }

      p:nth-of-type(1) {
        width: 120px;
      }

      p:nth-of-type(2) {
        width: 105px;
      }

      .btns {
        width: 106px;

        span:nth-of-type(1) {
          text-align: center;
          width: 72px;
          height: 28px;
          line-height: 28px;
          color: #fff;
          background: #cbcaca;
          cursor: default;
          border-radius: 3px;
        }
      }

      .evaluate-btn {
        width: 106px;

        span.appraise {
          display: flex;
          justify-items: center;
          cursor: pointer;
          height: 110px;
          line-height: 110px;
          align-items: center;
          border-bottom: 1px solid #e7e6e6;

          span {
            border-radius: 3px;
            margin: 0 auto;
            width: 72px;
            color: #fff;
            height: 28px;
            line-height: 28px;
            background: #ff6000;
            text-align: center;
          }

          .already-evaluated {
            background: #cbcaca;
          }
        }

        span.appraise:last-child {
          border-bottom: 0px;
        }
      }

      .receiving-goods {
        text-align: center;
        width: 72px;
        height: 28px;
        line-height: 28px;
        color: #fff;
        cursor: default;
        border-radius: 3px;
        margin-top: 0.1rem;
        background: #ff6000 !important;
      }

      .quxiao {
        background: #ff6000 !important;
        cursor: pointer !important;
      }

      .queren {
        background: #66b6ff !important;
      }
    }

    .youhui {
      line-height: 35px;
      color: #333;
      font-size: 12px;
      margin-left: 13px;

      span {
        color: #d02629;
      }
    }
  }
}
</style>
