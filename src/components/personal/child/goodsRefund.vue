<template>
  <div class="tuiKuan l">
    <div class="Box">
      <div class="top">
        <span>退款退货</span>
      </div>
      <div class="dingdan l">订单编号<input type="text" /></div>
      <div class="tuikuan l">退款退货单编号：<input type="text" />
        <span>搜索</span>
      </div>
    </div>
    <div class="head">
      <p>宝贝</p>
      <p>退款说明</p>
      <p>退款金额</p>
      <p>申请时间</p>
      <p>商品状态</p>
      <p>交易操作</p>
    </div>
    <div class="down" v-for="(item,index) in OrderReturnList" :key="index">
      <div class="up">
        <span>订单编号：{{item.order_sn_id}}</span>
        <span>退款编号：{{item.id}}</span>
        <span>店铺：
                  <router-link class="a-class" target="_blank" :to="{path:'/storehome',query:{id:item.store_id}}">{{item.shop_name}}</router-link>
              </span><img src="../../../assets/img/qq.jpg" />
      </div>
      <div class="under">
        <p>
          <router-link target="_blank" class="a-class" :to="{path:'/shopsn_product',query:{id:item.goods_id}}">
            <img :src="URL+item.pic_url" />
          </router-link>
          <router-link target="_blank" class="a-class" :to="{path:'/shopsn_product',query:{id:item.goods_id}}">
            <span>{{item.title}}</span>
          </router-link>
        </p>
        <p>
          <span>{{item.explain}}</span>
        </p>
        <p>
          <span>￥{{item.price}}</span>
        </p>
        <p>
          <span>{{item.create_time}}</span>
        </p>
        <p>
          <a @click="returnStatus(item)" v-if="item.is_receive == 1">卖家已收货</a>
          <a @click="returnStatus(item)" v-else>{{examineState[item.status]}}</a>
        </p>
        <p>
          <router-link target="_blank" :to="{path:'/xiangQing',query:{id:item.id}}">详情</router-link>
        </p>
      </div>
    </div>
    <div class="pagation" v-if="paging">
      <el-pagination background layout="prev, pager, next" :total="~~paging.count" :page-size="10" @current-change="handleCurrentChange">
      </el-pagination>
    </div>
  </div>
</template>

<script>
  export default {
    data() {
      return {
        OrderReturnList: [],
        examineState: {
          '0': '审核中',
          '1': '审核失败',
          '2': '审核通过',
          '3': '退货中',
          '4': '退货完成',
          '5': '已撤销'
        },
        paging: '',
        currentPage: 1,
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
      //退货-退货列表,搜索接口
      if(localStorage.getItem("loginuserdata") == 'true') {
        this.getOrderReturnList();
            let title = sessionStorage.getItem('titleKey') + '-' +sessionStorage.getItem('updateDescription');
          this.showScroll.scrollTitle(title);
      } 
      // else {
      //       this.$router.push('/passwordLogin')
      // }     
        this.getFootData();
        this.getFavIcon();   
    },
    mounted() {
        var self = this
            window.setTimeout(function () {
                self.headParams.title = sessionStorage.titleKey
                self.headParams.description = sessionStorage.updateDescription
                self.headParams.keywords = sessionStorage.contentKey
                self.headParams.link = sessionStorage.pcfavicon
                self.$emit('updateHead')
        }, 3000)
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
      returnStatus(item) {
        let link = '',
          s = item.status;
        if (item.is_receive == 1) {
          link = '/sellerReceives';
        } else {
          if (s == 0 || s == 1) {
            link = '/returnHandle';
          } else if (s == 2 || s == 3) {
            link = '/sellerReceives';
          } else if (s == 4) {
            link = '/sellerRefund';
          } else {
            link = '/apply';
          }
        }
        let refundInfo = {
          order_id: item.order_id,
          goods_id: item.goods_id,
          refund_id: item.id,
          store_id: item.store_id
        }
        localStorage.setItem('refundInfo', JSON.stringify(refundInfo));
        window.open(window.location.origin + link);
      },
      //退货-退货列表,搜索接口
      getOrderReturnList() {
        this.HTTP(this.$httpConfig.getOrderReturnList, {
          page: this.currentPage
        }, 'post').then((res) => {
          this.OrderReturnList = res.data.data.data;
          this.paging = res.data.data;
        }).catch((e)=>{
          console.log(e)
        })
      },
      handleCurrentChange(val) {
        this.currentPage = val;
        this.getOrderReturnList();
  
      }
    }
  }
</script>

<style lang="less" scoped>
  .pagation {
    text-align: center;
  }
  
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
  
  .tuiKuan {
    height: auto;
    width: 980px;
    background: #fff;
    margin: 16px 0;
    .top {
      overflow: hidden;
      margin: 14px 17px 20px;
      border-bottom: 1px solid #e7e7e7;
      line-height: 37px;
      span {
        display: inline-block;
        width: 104px;
        text-align: center;
        border-bottom: 2px solid #d02629;
        color: #d02629;
      }
    }
    .Box {
      overflow: hidden;
    }
    .dingdan {
      margin: 21px 35px 0 18px;
      font-size: 13px;
      color: #484848;
      input {
        width: 148px;
        height: 26px;
        border: 1px solid #bfbfbf;
        margin-left: 10px;
      }
    }
    .tuikuan {
      margin-top: 21px;
      font-size: 13px;
      color: #484848;
      input {
        width: 148px;
        height: 26px;
        border: 1px solid #bfbfbf;
        margin-right: 10px;
        margin-left: 10px;
      }
      span {
        width: 54px;
        height: 28px;
        border-radius: 3px;
        text-align: center;
        line-height: 28px;
        background: #27a9e3;
        color: #fff;
        display: inline-block;
        cursor: pointer;
      }
    }
    .head {
      display: block;
      height: 42px;
      line-height: 42px;
      background: #fafafa;
      border: 1px solid #e8e8e8;
      margin: 20px 17px 10px;
      p {
        float: left;
        font-size: 12px;
        color: #484848;
      }
      p:nth-of-type(1) {
        margin: 0 120px;
      }
      p:nth-of-type(3) {
        margin: 0 100px;
      }
      p:nth-of-type(4) {
        margin: 0 20px;
      }
      p:nth-of-type(5) {
        margin: 0 90px 0 71px;
      }
    }
    .down {
      margin: 0 17px 20px;
      width: 946px;
      overflow: hidden;
      .a-class {
        color: #484848;
      }
      .a-class:hover {
        color: red;
      }
      .up {
        float: left;
        width: 100%;
        height: 40px;
        border: 1px solid #91cfff;
        background: #eaf8ff;
        line-height: 40px;
        span {
          float: left;
          color: #484848;
          font-size: 12px;
          margin-right: 22px;
        }
        span:nth-of-type(1) {
          margin-left: 15px;
        }
        img {
          float: left;
          margin-top: 8px;
        }
      }
      .under {
        float: left;
        width: 100%;
        height: 107px;
        border: 1px solid #e8e8e8;
        border-top: 0;
        p {
          float: left;
          font-size: 12px;
          color: #484848;
          span {
            margin-top: 17px;
            float: left;
          }
        }
        p:nth-of-type(1) {
          margin-right: 30px;
          img {
            float: left;
            margin: 14px 11px 0 15px;
            width: 80px;
            height: 80px;
          }
          span {
            width: 135px;
            display: -webkit-box;
            overflow: hidden;
            white-space: normal!important;
            text-overflow: ellipsis;
            word-wrap: break-word;
            -webkit-line-clamp: 3;
            -webkit-box-orient: vertical;
          }
        }
        p:nth-of-type(2) {
          margin-right: 60px;
          span {
            width: 100px;
            display: -webkit-box;
            overflow: hidden;
            white-space: normal!important;
            text-overflow: ellipsis;
            word-wrap: break-word;
            -webkit-line-clamp: 3;
            -webkit-box-orient: vertical;
          }
        }
        p:nth-of-type(3) {
          margin-right: 115px;
          span {
            color: #ff6000;
          }
        }
        p:nth-of-type(5) {
          width: 65px;
          text-align: center;
          margin: 0 40px;
          a {
            display: inline-block;
            margin-top: 17px;
            color: #484848;
          }
          a:hover {
            color: red;
          }
        }
        p:nth-of-type(6) {
          float: right;
          margin-right: 20px;
          a {
            margin-top: 20px;
            display: inline-block;
            width: 49px;
            height: 25px;
            text-align: center;
            line-height: 25px;
            color: #fff;
            background: #27a9e3;
            border-radius: 3px;
          }
        }
      }
    }
  }
</style>