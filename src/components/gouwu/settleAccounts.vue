<template>
	<div class="account">
		<!-- <common-header></common-header> -->
		<common-header v-on:childToParent="onChildClick"></common-header>
		<div class="logo">
			<router-link to="/home">
				<img :src="URL + logoPhoto" />
			</router-link>
			<p>结算页</p>
		</div>
		<div class="center">
			<ul class="buzhou">
				<li class="l active">1</li>
				<li class="l active">2</li>
				<li class="l">3</li>
			</ul>
			<ul class="zhushi">
				<li class="l actives">立即购买</li>
				<li class="l actives">确认订单信息</li>
				<li class="l">成功提交订单</li>
			</ul>
			<address-operation @transferAddress="transferAddress"></address-operation>
			<div class="xinxi">
				<p class="biaoti">确认订单信息</p>
				<div class="bottom">
					<div class="thead">
						<p class="l">店铺宝贝</p>
						<p class="l">商品属性</p>
						<p class="l">单价</p>
						<p class="l">数量</p>
						<p class="l">优惠方式</p>
						<p class="l">小计</p>
					</div>
					<div class="dianpu">
						<p class="name">店铺：<span @click="toStore">{{storeData.shop_name}}</span><img src="../../assets/img/qq.jpg" /></p>
						<ul class="order-list">
							<li class="order-info">
								<img @click="toDetails" class="l" :src="URL + goodsImg.pic_url" alt="" />
								<p @click="toDetails" class="l">{{goodsData.title}}</p>
								<p class="l">无</p>
								<p class="l">{{goodsData.price_member}}</p>
								<p class="l">{{goodsData.goods_num}}</p>
								<p class="l">无</p>
								<p class="r">{{total|keep2Num}}</p>
							</li>
							<li class="fapiao">发票信息：<span class="invoice-info" v-if="invoiceTit[storeData.id]">{{invoiceTit[storeData.id][0]}}</span><span class="invoice-info" v-if="invoiceTit[storeData.id]">{{invoiceTit[storeData.id][1]}}</span><span class="invoice-info" v-if="invoiceTit[storeData.id]">{{invoiceTit[storeData.id][2]}}</span>
								<span class="invoice-toggle" @click="toggle(storeData.id)" v-if="invoiceState[storeData.id] == 1">修改</span>
								<span class="invoice-toggle" @click="toggle(storeData.id)" v-else>无需发票</span>
							</li>
							<li class="order-item clearfix">
								<div class="order-memo l">
									<div class="memo l">
										<span class="memo-title">给卖家留言：</span>
										<textarea v-model="remarks" class="text-area-input" placeholder=""></textarea>
									</div>
								</div>
								<div class="order-extInfo r">
									<div class="order-deliveryMethod">
										<span>运送方式：</span><span>普通运送 </span><span>快递 </span><span>免邮 </span> <span class="freight">{{freight_price|keep2Num}}</span>
									</div>
									<div class="shop-total">
										店铺合计（含运费）<span class="total-price">￥{{(total+freight_price)|keep2Num}}</span>
									</div>
								</div>
							</li>
						</ul>
					</div>
					<!--<div class="jifen">使用积分</div>-->
					<!--<div class="dikou l">-->
						<!--<p class="l">账户共<span>200</span>积分 本次使用<input type="text" /> 积分</p>-->
						<!--<p class="l">抵扣<span>￥20.00</span></p>-->
					<!--</div>-->
					<!--<div class="l helpjf">积分抵用<span>￥20.00</span></div>-->
				</div>
			</div>
			<div class="jiesuan">
				<p><span>{{goodsData.goods_num}} </span>件商品，商品总金额：￥{{total|keep2Num}}</p>
				<p>积分：-￥0.00</p>
				<p>优惠券：-￥0.00</p>
				<p>运费：￥{{freight_price|keep2Num}}</p>
			</div>
			<div class="pay">
				<p class="yingfu r">应付金额：<span>￥{{(total+freight_price)|keep2Num}}</span></p>
				<div class="r">
					<p class="l">寄送至：<span>{{addressInfo.prov_name}}{{addressInfo.city_name}}{{addressInfo.dist_name}} {{addressInfo.address}}</span></p>
					<p class="l">收货人：<span>{{addressInfo.realname}} {{addressInfo.mobile}}</span></p>
				</div>
			</div>
			<div class="submit">
				<p class="r"><a @click="placeOrder">提交订单</a></p>
			</div>
		</div>
		<!--弹出内容    修改-->
		<modal :backdrop-closable="false" title="发票信息" :width="625" :is-show="isShow" transition="fadeDown" @close="isShow=false" :show-footer="false">
			<div class="alert">
				<div class="fapiao"><span v-for="(item,index) in invoiceData.invoice_type" :key="index" @click="Fun(index,item.name)" :class="{bg:isbg0==index}">{{item.name}}</span></div>
				<div class="taitou">
					<p class="l">发票抬头：</p>
					<div class="l inp" ref="inp">
						<div class="value" v-for="(item,index) in invoiceData.raised_data" :key="item.id" @click="fun1(index,item.name)" :class="{bg:isbg1==index}">
							<input type="text" :readonly="item.no_editable" v-model="item.name" placeholder="请填写公司发票抬头" />
							<span v-if="item.no_editable" @click="insert(index)">编辑</span>
							<span v-if="item.no_editable" @click="del(index,item.id)">删除</span>
							<span v-if="!item.no_editable" @click="keep(index)">保存</span>
						</div>
					</div>
					<p class="l" @click="add" v-show="news">新增单位发票</p>
				</div>
				<div class="content">
					<p class="l">发票内容：</p>
					<span class="l" v-for="(item,index) in invoiceData.invoice_content" :key="item.id" @click="fun2(index,item.name)" :class="{bg:isbg2==index}">{{item.name}}</span>
				</div>
				<div class="btn"><button @click="save">开发票</button><button @click="cancel">无需发票</button></div>
			</div>
		</modal>
		<foot-com></foot-com>
	</div>
</template>
<script>
	import chahu from '@/assets/img/buychahu.jpg'
	import QS from 'qs';
	export default {
		data() {
			return {
				isShow: false,
				isbg0: null,
				isbg1: null,
				isbg2: null,
				news: true,
				storeData: {},
				goodsData: {},
				goodsImg: {},
				total: 0,
				addressInfo: {},
				remarks: '',
				invoiceData: {},
				invoice_content: '',
				invoice_type: '',
				raised_data: '',
				content_id: '',
				type_id: '',
				raised_id: '',
				content_name: '',
				type_name: '',
				raised_name: '',
				freight_price: 0,
				not_distribution: true,
				store_id: '',
				invoiceInit: {},
				invoiceGroup: {},
				invoiceState: [],
				invoiceTit: {},
				fromChild: '',
				logoPhoto:'',
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
			this.getBuyImmediately();
			this.func();
			this.getFootData();
      		this.getFavIcon(); 
			let title=sessionStorage.getItem('titleKey') + '-' +sessionStorage.getItem('updateDescription');
			this.showScroll.scrollTitle(title);
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
						this.logoPhoto = res.data.data.logo_name;
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
		
			// 禁止浏览器后退
			func(){
				history.pushState(null, null, document.URL);
				window.addEventListener('popstate', function () {
					history.pushState(null, null, document.URL);
				});
			},
			cancel() {
				this.isShow = false;
				this.invoiceGroup[this.store_id] = {
					translate: 0
				};
				delete this.invoiceInit[this.store_id];
				this.invoiceOff();
				delete this.invoiceTit[this.store_id];
				this.type_name = '';
				this.raised_name = '';
				this.content_name = '';
			},
			save() {
				if (this.type_name == '' || this.raised_name == '' || this.content_name == '') {
					alert('请完善发票信息');
					return false;
				}
				this.getInvoiceId(this.content_id, this.type_id, this.raised_id);
			},
			placeOrder() {
				if (!this.addressInfo.id) {
					this.$alert('当前收货人信息不对，请修改后再提交', '提示', {
						confirmButtonText: '确定',
						center: true,
						lockScroll: false,
						type: 'warning'
					})
					return false;
				}
				if (!this.not_distribution) {
					this.$alert('当前地址卖家暂无配送', '提示', {
						confirmButtonText: '确定',
						center: true,
						lockScroll: false,
						type: 'warning'
					})
					return false;
				}
				if (Object.keys(this.invoiceGroup).length === 0) {
					this.invoiceGroup[this.storeData.id] = {
						translate: 0
					};
				}
				let total = this.total + this.freight_price;
				this.HTTP(this.$httpConfig.orderBeginFromPanicGood, {
					invoice_id: this.invoiceGroup,
					address_id: this.addressInfo.id,
					remarks: this.remarks,
					goods_id: this.$route.query.id,
					goods_num: this.$route.query.num,
					panic_id: this.goodsData.panic_id
				}, 'post').then((res) => {
					this.$router.push({
						path: '/payment',
						query: {
							total_price: total
						}
					})
				}).catch((res) => {
					this.$alert(res.data.message, '提示', {
						confirmButtonText: '确定',
						center: true,
						lockScroll: false,
						type: 'warning'
					})
				})

			},
			// 发票类型
			getInvoiceType() {
				this.HTTP(this.$httpConfig.getInvoiceTypeData, {}, 'post').then((res) => {
					this.invoiceData = res.data.data;
					if (this.invoiceData.raised_data.length) {
						for (let index = 0; index < this.invoiceData.raised_data.length; index++) {
							this.$set(this.invoiceData.raised_data[index], 'no_editable', true)
						}
					}
				})
			},
			//获取发票id
			getInvoiceId(content_id, type_id, raised_id) {
				this.HTTP(this.$httpConfig.addOrderInvoice, {
					raised_id: parseInt(raised_id),
					content_id: parseInt(content_id),
					type_id: parseInt(type_id)
				}, 'post').then((res) => {
					this.invoiceGroup[this.store_id] = {
						id: res.data.data,
						translate: 1
					};
					this.invoiceInit[this.store_id] = [this.isbg0, this.isbg1, this.isbg2];
					this.invoiceOff();
					this.invoiceTit[this.store_id] = [this.type_name, this.raised_name, this.content_name];
					// this.type_name = '';
					// this.raised_name = '';
					// this.content_name = '';
					this.isShow = false;
				}).catch(() => {

				})
			},
			// 无需发票和修改显示
			invoiceOff() {
				let listKey = Object.keys(this.invoiceGroup);
				for (const i in listKey) {
					if (this.invoiceGroup[listKey[i]]) {
						this.invoiceState[listKey[i]] = this.invoiceGroup[listKey[i]].translate;
					}
				}
			},
			toDetails() {
				window.open(window.location.origin + '/shopsn_product/' + this.goodsData.id);
			},
			toStore() {
				window.open(window.location.origin + '/storehome/' + this.storeData.id);
			},
			//地址
			transferAddress(address) {
				this.addressInfo = address;
				this.getFreight();
			},
			//获取运费
			getFreight() {
				this.HTTP(this.$httpConfig.getFreight, {
					prov_id: this.addressInfo.prov,
					city_id: this.addressInfo.city,
					dist_id: this.addressInfo.dist
				}, 'post').then((res) => {
					this.not_distribution = true;
					this.freight_price = parseFloat(res.data.data);
				}).catch((res) => {
					this.not_distribution = false;
					this.$alert('当前地址卖家暂无配送', '提示', {
						confirmButtonText: '确定',
						center: true,
						lockScroll: false,
						type: 'warning'
					})
				})
			},
			//订单数据
			getBuyImmediately() {
					var res = JSON.parse(sessionStorage.getItem('rusToBuy'));
					this.storeData = res.store;
					this.goodsData = res.goods;
					this.goodsImg = res.img;
					this.total = parseFloat(this.goodsData.price_member) * parseFloat(this.goodsData.goods_num);
			},
			toggle(id) {
				this.store_id = id;
				this.isbg0 = null;
				this.isbg1 = null;
				this.isbg2 = null;
				let obj = Object.keys(this.invoiceInit);
				if (obj.length > 0 && this.invoiceInit[id]) {
					this.isbg0 = this.invoiceInit[id][0];
					this.isbg1 = this.invoiceInit[id][1];
					this.isbg2 = this.invoiceInit[id][2];
				}
				this.getInvoiceType();
				this.isShow = !this.isShow;
			},
			Fun(index, name) {
				this.type_id = this.invoiceData.invoice_type[index].id;
				this.type_name = name;
				this.isbg0 = index;
			},
			fun1(index, name) {
				this.raised_id = this.invoiceData.raised_data[index].id;
				this.raised_name = name;
				this.isbg1 = index;
			},
			fun2(index, name) {
				this.content_id = this.invoiceData.invoice_content[index].id;
				this.content_name = name;
				this.isbg2 = index;
			},
			add() {
				let data = {
					name: '',
					no_editable: false
				}
				this.invoiceData.raised_data.push(data)
				this.$nextTick(() => {
					this.$refs.inp.scrollTop = this.$refs.inp.scrollHeight;
				})
				this.news = false

			},
			insert(index) {
				this.$set(this.invoiceData.raised_data[index], 'no_editable', false);
			},
			del(index, id) {
				this.isShow = false;
				this.$confirm('您确定要删除该发票信息吗？', '提示', {
					confirmButtonText: '确定',
					cancelButtonText: '取消',
					lockScroll: false,
					type: 'warning',
					closeOnClickModal: false,
					center: true
				}).then(() => {
					this.HTTP(this.$httpConfig.invoicesAreRaisedDelete, {
							id: this.invoiceData.raised_data[index].id
						}, 'post')
						.then((res) => {
							if (this.invoiceData.raised_data[index].id == id) {
								this.invoiceData.raised_data.splice(index, 1);
								this.isbg1 = null;
								this.raised_id = '';
								this.raised_name = '';
							}
							this.isShow = true;
						})
				}).catch(() => {
					this.isShow = true;
				})
			},
			keep(index) {
				if (!this.invoiceData.raised_data[index].name) {
					alert('输入不能为空')
					return false;
				}
				if (this.invoiceData.raised_data[index].id) { //编辑
					this.HTTP(this.$httpConfig.invoicesAreRaisedSave, {
							id: this.invoiceData.raised_data[index].id,
							name: this.invoiceData.raised_data[index].name
						}, 'post')
						.then((res) => {
							this.$set(this.invoiceData.raised_data[index], 'name', this.invoiceData.raised_data[index].name);
							this.$set(this.invoiceData.raised_data[index], 'no_editable', true);
						})
				} else { //添加
					this.HTTP(this.$httpConfig.invoicesAreRaisedAdd, {
							name: this.invoiceData.raised_data[index].name
						}, 'post')
						.then((res) => {
							this.raised_id = res.data.data;
							let data = {
								id: res.data.data,
								name: this.invoiceData.raised_data[index].name,
								no_editable: true
							}
							for (let index = 0; index < this.invoiceData.raised_data.length; index++) {
								if (this.invoiceData.raised_data[index].no_editable === false) {
									this.invoiceData.raised_data.splice(index, 1);
								}
							}
							this.invoiceData.raised_data.push(data);
						})
				}
				this.news = true;
			},

		}

	}
</script>

<style>
	.el-message-box--center {
		padding-bottom: 30px!important;
	}

	.el-message-box--center .el-message-box__header {
		padding-top: 30px!important;
	}
</style>

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

	.logo {
		height: 115px;
		width: 1200px;
		margin: 0 auto;
		img {
			margin-top: 28px;
			float: left;
			width: 184px;
            height: 53px;
		}
		p {
			font-size: 24px;
			float: left;
			margin: 40px 0 0 29px;
		}
	}

	.buzhou {
		height: 50px;
		background: url(../../assets/img/buzhou.jpg) no-repeat center;
		li {
			width: 42px;
			height: 42px;
			border-radius: 50%;
			background: #878788;
			text-align: center;
			line-height: 42px;
			font-size: 20px;
			color: #fff;
			font-weight: 900;
			margin-top: 4px;
		}
		li:nth-of-type(1) {
			margin-left: 207px;
		}
		li:nth-of-type(2) {
			margin: 4px 353px 0 343px;
		}
		.active {
			background: #d02629 !important;
		}
	}

	.zhushi {
		height: 42px;
		li {
			font-size: 14px;
			color: #666;
			line-height: 42px;
		}
		li:nth-of-type(1) {
			margin-left: 192px;
		}
		li:nth-of-type(2) {
			margin: 0 310px 0 313px;
		}
		.actives {
			color: #d02629 !important;
		}
	}

	.dizhi {
		overflow: hidden;
		.biaoti {
			font-size: 14px;
			margin: 18px 0 20px;
			width: 100%;
		}
		.dizhiul {
			overflow: hidden;
			li {
				width: 279px;
				height: 123px;
				border: 1px solid #dedede;
				margin-right: 25px;
				cursor: pointer;
				margin-bottom: 10px;
				p {
					font-size: 12px;
					color: #333;
					padding-left: 13px;
					padding-right: 13px;
				}
				p:nth-of-type(1) {
					line-height: 38px;
					border-bottom: 1px solid #dedede;
				}
				p:nth-of-type(2) {
					margin: 16px 0 10px;
					height: 34px;
				}
				p:nth-of-type(3) {
					color: #d02629;
				}
			}
			.hover {
				border-color: #d02629 !important;
				background: url(../../assets/img/right.jpg) no-repeat right 0 bottom 0;
			}
			li:nth-child(4n) {
				margin-right: 0;
			}
		}
		.guanli {
			overflow: hidden;
			margin: 15px 0 12px 0;
			button {
				width: 84px;
				height: 25px;
				text-align: center;
				line-height: 25px;
				float: left;
				font-size: 12px;
				cursor: pointer;
			}
			button:nth-of-type(1) {
				background: #d02629;
				color: #fff;
				margin-right: 18px;
			}
			button:nth-of-type(2) {
				width: 84px;
				height: 25px;
				border: 1px solid #dedede;
				color: #333;
			}
		}
	}

	.xinxi {
		overflow: hidden;
		.biaoti {
			font-size: 12px;
			color: #262626;
			margin: 20px 0;
		}
		.bottom {
			border: 1px solid #eaeaea;
			overflow: hidden;
			.thead {
				height: 51px;
				border-bottom: 1px solid #eaeaea;
				margin: 0 38px;
				line-height: 60px;
				float: left;
				width: 1124px;
				p {
					font-size: 12px;
				}
				p:nth-of-type(2) {
					margin: 0 103px 0 300px;
				}
				p:nth-of-type(4) {
					margin: 0 135px 0 131px;
				}
				p:nth-of-type(6) {
					margin-left: 189px;
				}
			}
		}
		.dianpu {
			overflow: hidden;
			margin: 0 38px;
			float: left;
			margin-top: 18px;
			width: 1124px;
			.name {
				font-size: 12px;
				line-height: 32px;
				height: 30px;
				img {
					margin: -5px 0 0 12px;
				}
				span {
					cursor: pointer;
				}
				span:hover {
					color: red;
				}
			}
			.order-list {
				.order-info {
					height: 80px;
					background: #fcfcfc;
					margin-bottom: 1px;
					img {
						margin: 16px 9px 0 13px;
						width: 60px;
						height: 57px;
						cursor: pointer;
					}
					p {
						font-size: 12px;
						color: #666;
						margin-top: 17px;
					}
					p:nth-of-type(1) {
						width: 192px;
						margin-right: 93px;
						cursor: pointer;
					}
					p:nth-of-type(1):hover {
						color: red;
					}
					p:nth-of-type(3) {
						margin: 17px 147px 0 123px;
					}
					p:nth-of-type(5) {
						margin: 17px 0px 0 141px;
					}
					p:last-child {
						color: #dd2727;
						margin: 17px 47px 0 0;
					}
				}
				select {
					margin: 17px 0 0 108px;
				}
				.fapiao {
					height: 45px;
					line-height: 45px;
					font-size: 12px;
					color: #616161;
					padding-left: 15px;
					.invoice-info {
						color: #313131;
						margin-right: 5px;
					}
					.invoice-toggle {
						color: #378ce4;
						cursor: pointer;
					}
					.invoice-toggle:hover {
						color: red;
					}
				}
				.sty {
					width: 200px;
					height: 20px;
					border: 1px solid #d9d9d9;
					line-height: 19px;
					background: #fff;
					padding-left: 10px;
				}
				.order-item {
					padding-left: 15px;
					.order-memo {
						width: 50%;
						.memo {
							padding: 10px 0;
							.memo-title {
								color: #3c3c3c;
								font-size: 12px;
							}
							.text-area-input {
								vertical-align: top;
								width: 328px;
								resize: none;
								height: 20px;
								line-height: 18px;
								text-indent: 4px;
								border: 1px solid #ccc;
								outline: 0;
								overflow: auto;
							}
							.text-area-input:focus {
								height: 59px;
							}
						}
					}
					.order-extInfo {
						width: 50%;
						height: 79px;
						text-align: right;
						div {
							padding: 10px 0;
							font-size: 12px;
							.freight {
								color: #dd2727;
								margin: 0 45px 0 53px;
							}
							.total-price {
								font-size: 13px;
								color: #df7344;
								margin: 0 40px 0 10px;
							}
						}
					}
				}
			}
		}
		.jifen {
			margin-top: 31px;
			font-size: 12px;
			color: #363636;
			border-bottom: 1px solid #eaeaea;
			float: left;
			width: 1124px;
			margin-left: 37px;
			height: 21px;
		}
		.dikou {
			height: 44px;
			line-height: 44px;
			width: 1124px;
			margin-left: 37px;
			p {
				font-size: 12px;
				color: #363636;
				margin-right: 21px;
				input {
					width: 86px;
					height: 22px;
					border: 1px solid #e3e3e3;
					margin: 0 10px 0 6px;
				}
				span {
					color: #f28765;
				}
			}
		}
		.helpjf {
			height: 44px;
			line-height: 44px;
			width: 1124px;
			margin-left: 37px;
			background: #fcfcfc;
			padding-left: 16px;
			font-size: 12px;
			color: #363636;
			span {
				color: #dd2727;
			}
		}
	}

	.jiesuan {
		margin-top: 30px;
		p {
			text-align: right;
			width: 100%;
			font-size: 12px;
			color: #484848;
			margin-bottom: 7px;
			span {
				color: #df7344;
			}
		}
	}

	.pay {
		height: 100px;
		background: #eaeaea;
		text-align: right;
		.yingfu {
			font-size: 15px;
			color: #2e2e2e;
			margin: 23px 25px 0 0;
			width: 100%;
			span {
				font-size: 19px;
				color: #e82825;
			}
		}
		div.r {
			margin-top: 20px;
			p {
				font-size: 12px;
				color: #656565;
				margin-right: 23px;
			}
		}
	}

	.submit {
		overflow: hidden;
		margin-top: 15px;
		margin-bottom: 136px;
		p {
			width: 182px;
			height: 38px;
			background: #ff5100;
			text-align: center;
			line-height: 38px;
			color: #fff;
			cursor: pointer;
			a {
				display: block;
				width: 100%;
				height: 100%;
				color: #fff;
			}
		}
	}

	.alert {
		margin: 20px 0 200px 25px;
		.fapiao {
			overflow: hidden;
			span {
				width: 96px;
				height: 30px;
				float: left;
				border: 1px solid #ccc;
				text-align: center;
				line-height: 30px;
				font-size: 12px;
				color: #646464;
				margin-right: 14px;
				cursor: pointer;
			}
		}
		.taitou {
			overflow: hidden;
			margin-top: 20px;
			margin-bottom: 22px;
			p:nth-of-type(1) {
				font-size: 12px;
				color: #646464;
				margin-top: 7px;
			}
			.inp {
				width: 376px;
				overflow: auto;
				overflow-x: hidden;
				max-height: 126px;
				min-height: 42px;
			}
			.top {
				width: 338px;
				height: 32px;
				line-height: 32px;
				border: 1px solid #ccc;
				padding-left: 13px;
				margin-bottom: 10px;
				float: left;
				cursor: pointer;
			}
			.value {
				width: 338px;
				height: 32px;
				border: 1px solid #ccc;
				padding-left: 13px;
				margin-bottom: 10px;
				float: left;
				cursor: pointer;
				input {
					width: 247px;
					height: 30px;
				}
				span {
					font-size: 12px;
					color: #43628e;
					padding-right: 8px;
				}
				span:hover {
					color: red;
				}
			}
			P:nth-of-type(2) {
				font-size: 12px;
				color: #4e75ac;
				margin-left: 60px;
				cursor: pointer;
			}
			P:nth-of-type(2):hover {
				color: red;
			}
		}
		.content {
			overflow: hidden;
			p {
				font-size: 12px;
				color: #646464;
				margin-top: 7px;
			}
			span {
				width: 83px;
				height: 30px;
				float: left;
				border: 1px solid #ccc;
				margin-right: 15px;
				text-align: center;
				line-height: 30px;
				font-size: 12px;
				cursor: pointer;
			}
		}
		.btn {
			button {
				width: 70px;
				height: 30px;
				border: 1px solid #ccc;
				border-radius: 3px;
				margin-right: 8px;
				cursor: pointer;
			}
			button:nth-of-type(1) {
				color: #fff;
				background: #d02629;
			}
			button:nth-of-type(2) {
				background: #eaeaea;
				cursor: pointer;
			}
		}
		.bg {
			background: url(../../assets/img/bg.jpg) no-repeat bottom 0 right 0;
			border-color: #d02629 !important;
		}
	}
</style>
