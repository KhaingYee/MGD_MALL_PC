<template>
	<div class="apply">
		<!-- <common-header></common-header> -->
		<common-header v-on:childToParent="onChildClick"></common-header>
		<my-header></my-header>
		<div class="center">
			<div class="top"><span>您的位置： </span> <span><router-link to='/myOrder'>交易中心</router-link></span> > <span><router-link to='myOrder'>我的订单</router-link> </span> > <span>申请售后</span></div>
			<div class="middle">
				<div class="left l">
					<div class="up"><span>服务类型</span></div>
					<div class="down">
						<router-link v-if="order_info.status== 4"  :to="{name:'returnGoods',params:{id:$route.params.id,goods_id:$route.params.goods_id,order_sn_id:$route.params.order_sn_id}}">退款退货</router-link>
            <a v-else class="prohibit">退款退货</a>
						 <router-link  :to="{name:'applyRefund',params:{id:$route.params.id,goods_id:$route.params.goods_id,order_sn_id:$route.params.order_sn_id,type:1}}">仅退款</router-link>
						<router-link v-if="order_info.status== 4" :to="{name:'applyChange',params:{id:$route.params.id,goods_id:$route.params.goods_id,order_sn_id:$route.params.order_sn_id,type:1}}">换货</router-link>
            <a v-else class="prohibit">换货</a>
          </div>
				</div>
				<div class="right r">
					<div class="xiangqing">订单详情</div>
					<div class="shangpin"><img :src="URL + img.pic_url" /><span>{{goods.title}}</span></div>
					<ul class="ul">
						<li>卖 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 家：{{store_info.shop_name}}</li>
						<li>订单编号：<span class="lanse">{{$route.params.order_sn_id}}</span></li>
						<li>单 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 价：<span>{{order_info.goods_price}}</span> 元* {{order_info.goods_num}} (数量)</li>
					</ul>
				</div>
			</div>
			<div class="bottom">
        <div class="jieshao"><span>服务介绍</span></div>
        <p>1.仅退款</p><span class="spa">申请条件：若您未收到货，或已收到货且与卖家达成一致不退货仅退款时，请选择“仅退款”选项。</span><span class="spa">退款流程：1.申请退款>2.卖家同意退款申请>3.退款成功</span>
        <p>2.退货退款</p><span class="spa">申请条件：若为商品问题，或者不想要了且与卖家达成一致退货，请选择“退货退款”选项，退货后请保留物流底单（闲置商品和未加入消保商品仅支持未收到货的退款）。</span><span class="spa">退货流程：1.申请退货>2.卖家发送退货地址给买家>3.买家退货并填写退货物流信息>4.卖家确认收货，退款成功</span>
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
				order_info: {},
				store_info: {},
				goods: {},
				img:{},
				goods:{},
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
			this.orderInfo();
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
			onChildClick (value) {
				this.fromChild = value
				if(this.fromChild == 'false') {
					location.reload();
				}
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
				}).catch((e)=>{
					console.log(e)
				})
			}
		},
		components: {
			"my-header": myHeader
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

	.apply {
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

	.middle {
		overflow: hidden;
		.left {
			width: 902px;
			height: 339px;
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
				text-align: center;
				margin-top: 56px;
				a {
					width: 138px;
					height: 38px;
					background: #d02629;
					text-align: center;
					line-height: 38px;
					color: #ffffff;
					display: inline-block;
					border-radius: 3px;
					margin-right: 50px;
					border: 1px solid #f8c44e;
				}
				.prohibit{
					cursor: no-drop;
					background: #999;
					border-color: #757575;
					color: #333;
				}
			}
		}
		.right {
			width: 289px;
			height: 339px;
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
					width: 52px;
					height: 52px;
					margin: 0 11px 15px 0;
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
</style>
