<template>
	<div class="xiangqing">
		<!-- <common-header></common-header> -->
    <common-header v-on:childToParent="onChildClick"></common-header>
		<my-header></my-header>
		<div class="center">
			<div class="top">
				<span>您的位置： </span>
				<span>交易中心</span> >
				<span>退款退货 </span> >
				<span>查看详情</span>
			</div>
			<div class="bottom">
				<div class="left l">
					<p>{{examineState[info.status]}}</p>
					<ul>
						<li>订单编号：{{info.order_id}}</li>
						<li>退款编号：{{info.id}}</li>
						<li v-if="info.create_time">申请时间：{{info.create_time}}</li>
						<!-- <li>退款成功时间：2017-04-13 16</li> -->
						<li>退款金额：
							<span>{{info.price}}</span> 元</li>
						<li>退款原因：{{info.explain}}</li>
						<li>退款说明：退款由系统自动完成</li>
					</ul>
				</div>
				<div class="right r">
					<div class="head">退款退货申请</div>
					<div class="shangpin"><img :src="URL+info.pic_url" />
						<span>{{info.title}} </span>
					</div>
					<ul class="ul">
						<li>卖 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 家：{{info.shop_name}}</li>
						<li>联系电话：{{info.mobile}}</li>
						<li>单 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 价：
							<span>{{info.price_member}}</span>元*1（数量）</li>
						<li>邮 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 费：
							<span>{{info.shipping_monery}}</span>元</li>
						<li>成交时间：{{info.pay_time | formatDate}}</li>
					</ul>
				</div>
			</div>
		</div>
		<foot-com></foot-com>
	</div>
</template>
 
<script>
import myHeader from "./header/myHeader.vue"; //个人中心的头部
export default {
	data() {
		return {
			info: {},
      examineState: {
          '0': '审核中',
          '1': '审核失败',
          '2': '审核通过',
          '3': '退货中',
          '4': '退货完成',
          '5': '已撤销'
      },
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
      this.getInfo();
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
  components: {
    "my-header": myHeader
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
		getInfo() {
			this.HTTP(this.$httpConfig.orderReturnDetails, { id: this.$route.query.id }, 'post').then((res) => {
				this.info = res.data.data;
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
.xiangqing {
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
.bottom {
  overflow: hidden;
  margin-bottom: 31px;
  .left {
    width: 902px;
    height: 480px;
    background: #fff;
    p {
      font-size: 18px;
      margin: 46px 0 22px 43px;
    }
    ul {
      margin-left: 43px;
      li {
        line-height: 28px;
        font-size: 14px;
        color: #333;
        span {
          color: #ff6000;
        }
      }
    }
  }
  .right {
    width: 289px;
    height: 480px;
    background: #fff;
    .head {
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
		width: 50px;
		height: 50px;
        float: left;
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
  }
}
</style>