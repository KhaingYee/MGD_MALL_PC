<template>
	<div class="mycoupons l">
		<div class="top">
      <ul>
        <li @click="Copfen(0)" :class="{color:isColor==0}">
          <p>我的优惠券</p>
        </li>
        <li @click="Copfen(1)" :class="{color:isColor==1}">
          <p>已使用</p>
        </li>
        <li @click="Copfen(2)" :class="{color:isColor==2}">
          <p>已过期</p>
        </li>
      </ul>
		</div>
		<div class="content-coupon">
			<div class="l youhui youhuis" v-for="(item,index) in couponList" :key="index" v-if="item.goods[0]">
				<div class="up" :class="{grayColor:isColor==1 || isColor==2}">
					<p>
						<span class="l">￥</span>
						<span class="l">{{item.money}}</span>
						<span class="r">{{item.name}}</span>
					</p>
					<p>使用条件:满{{item.condition}}</p>
					<p>有效时间:{{item.use_start_time}} 至 {{item.use_end_time}}</p>
					<p>发行店铺:{{item.shop_name}}
            <span class="noava" v-show="isColor==1 || isColor==2">无法使用</span>
          </p>
					<ul>
						<li class="l" v-for="(val,index) in item.goods" :key="index" @click="nextproduct(val.p_id)"><img :src="URL + val.pic_url" />{{val.price_member}}</li>
						<!-- <li class="l"><img src="../../../assets/img/youhuihu.png" />318.00</li> -->
					</ul>
				</div>
				<div class="down" v-if="item.goods[0]">
          <img @click="toShop(item.store_id)" src="../../../assets/img/youhuicar.png" alt="" /> |
          <img @click="delCoupon(item.id)" src="../../../assets/img/youhuilajikuang.png" />
        </div>
			</div>
		</div>
    <!-- <div class="box2" style="width: 140px;margin: 0 auto;margin-top: 20px">
          <el-pagination @current-change="handleCurrentChange" background layout="total,prev, pager, next,jumper"
                         :current-page.sync="currentPage" :page-size="page_size" :total="page">
          </el-pagination>
    </div> -->
	</div>
</template>

<script>
export default {
	data() {
		return {
			couponList: [],
      isColor: 0,
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
        this.Copfen(0);
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
    nextproduct(id){
      window.open(
          window.location.origin + "/shopsn_product?id=" + id
      );
    },
    // handleCurrentChange(currentPage) { //下一页
    //   this.currentPage = currentPage;
    //   this.getCoupon();
    // },
    Copfen(index){
      this.isColor = index;
      this.status = index;
      this.getCoupon();
    },
		getCoupon() {
			this.HTTP(this.$httpConfig.myCouponLists, { status: this.status, page: 1 }, 'post')
				.then((res) => {
					this.couponList = res.data.data.list;
				}).catch((e)=>{
          console.log(e)
        })
    },
    toShop(id){
				window.open(window.location.origin+'/storehome?id='+id)
    },
    delCoupon(id){
      this.HTTP(this.$httpConfig.delCoupon, {c_id:id}, 'post')
      .then((res) => {
       	if (res.data.status == 1) {
						this.$message.success(res.data.message);
					}
      }).catch((e)=>{
        console.log(e)
      })
      this.getCoupon();
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

.mycoupons {
  min-height: 1100px;
  height: auto;
  width: 980px;
  background: #fff;
  margin: 16px 0;
  float: left;
  .top {
    overflow: hidden;
    margin: 14px 17px 0px;
    border-bottom: 1px solid #e7e7e7;
    line-height: 37px;
    ul {
      li {
        cursor: pointer;
        float: left;
        width: 104px;
        height: 37px;
        color: #333;
        p {
          margin-top: 10px;
          font-size: 14px;
          text-align: center;
          line-height: 16px;
          border-right: 1px solid #e8e8e8;
        }
      }
      .color {
        border-bottom: 2px solid #d02629;
        color: #d02629 !important;
      }
    }
  }
  .content-coupon {
	  width: 980px;
	  height: auto;
	  display: flex;
	  flex-direction: row;
	  flex-wrap: wrap;
	  padding: 0 0 20px 0;
    .youhui {
      margin: 18px 20px 0 0;
      .up {
        width: 243px;
        height: 260px;
        background: url(../../../assets/img/youhui.png) no-repeat center;
        p {
          float: left;
          width: 218px;
          margin-left: 12px;
          margin-right: 12px;
          font-size: 11px;
          color: #fff;
          line-height: 21px;
        }
        .noava{
          font-size: 11px;
          color: #fff;
          border: 1px solid #776e6e;
          background-color: #776e6e;
          padding: 1px 5px;
          border-radius: 6px;
          float: right;
        }
        p:nth-of-type(1) {
          margin: 19px 12px 23px 12px;
          span:nth-of-type(1) {
            font-size: 18px;
          }
          span:nth-of-type(2) {
            font-size: 28px;
          }
        }
        ul {
          float: left;
          margin-top: 36px;
          margin-left: 5px;
          li {
            width: 70px;
            height: 84px;
            text-align: center;
            font-size: 10px;
            color: #666;
            margin-left: 6px;
            img {
              float: left;
              margin-bottom: 4px;
            }
          }
        }
      }
      .grayColor{
        width: 243px;
        height: 260px;
        background: url(../../../assets/img/youhuino.png) no-repeat center;
        p {
          float: left;
          width: 218px;
          margin-left: 12px;
          margin-right: 12px;
          font-size: 11px;
          color: #fff;
          line-height: 21px;
        }
        .noava{
          font-size: 11px;
          color: #fff;
          border: 1px solid #776e6e;
          background-color: #776e6e;
          padding: 1px 5px;
          border-radius: 6px;
          float: right;
        }
        p:nth-of-type(1) {
          margin: 19px 12px 23px 12px;
          span:nth-of-type(1) {
            font-size: 18px;
          }
          span:nth-of-type(2) {
            font-size: 28px;
          }
        }
        ul {
          float: left;
          margin-top: 36px;
          margin-left: 5px;
          li {
            width: 70px;
            height: 84px;
            text-align: center;
            font-size: 10px;
            color: #666;
            margin-left: 6px;
            img {
              float: left;
              margin-bottom: 4px;
            }
          }
        } 
      }
    }
    .youhuis {
      margin-left: 22px !important;
    }
    .down {
      text-align: center;
      margin-top: 12px;
      color: #f3efee;
      img {
        cursor: pointer;
      }
    }
  }
}
</style>
