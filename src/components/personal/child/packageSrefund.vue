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
			<p>退款金额</p>
			<p>申请时间</p>
			<p>服务类型</p>
			<p>退款状态</p>
			<p>交易操作</p>
		</div>
		<div class="down" v-for="(item,index) in OrderReturnList" :key="index">
			<div class="up">
				<span>订单编号：{{item.order_sn_id}}</span>
				<span>退款编号：{{item.id}}</span>
				<span>店铺：{{item.shop_name}}</span><img src="../../../assets/img/qq.jpg" /></div>
			<div class="under">
				<p><img :src="URL+item.pic_url" />
					<span>{{item.title}}</span>
				</p>
				<p>
					<span>￥{{item.price}}</span>
				</p>
				<p>
					<span>{{item.create_time}}</span>
				</p>
				<p>
					<span>仅退款</span>
				</p>
				<p>
					<span>退款成功</span>
				</p>
				<p>
					<a @click="toDetail(item.id)">详情</a>
				</p>
			</div>
		</div>
	</div>
</template>

<script>
export default {
	data() {
		return {
			OrderReturnList:[],
			OrderExchangeList:[],
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
      if(localStorage.getItem("loginuserdata") == 'true') {
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
      this.getOrderReturnList();
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
		//退货-退货列表,搜索接口
		getOrderReturnList(){
			this.HTTP(this.$httpConfig.getOrderReturnList,{},'post').then((res)=>{
				this.OrderReturnList = res.data.data.data;
			}).catch((e)=>{
        console.log(e)
      })
		},
		toDetail(id){
			this.$router.push({
				name:'xiangQing',
				params:{
					id:id
				}
			})
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
      margin: 0 164px 0 192px;
    }
    p:nth-of-type(3) {
      margin: 0 90px 0 75px;
    }
    p:nth-of-type(5) {
      margin: 0 54px 0 71px;
    }
  }
  .down {
    margin: 0 17px 20px;
    width: 946px;
    overflow: hidden;
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
        margin-right: 126px;
        img {
          float: left;
		  margin: 14px 11px 0 15px;
		  width: 80px;
		  height: 80px;
        }
        span {
		  width: 150px;
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
        margin-right: 82px;
        span {
          color: #ff6000;
        }
      }
      p:nth-of-type(4) {
        margin: 0 80px 0 74px;
        span {
          color: #ff6000;
        }
      }
      p:nth-of-type(5) {
        span {
          color: #ff6000;
        }
      }
      p:nth-of-type(6) {
        float: right;
        margin-right: 30px;
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