<template>
	<div id="interview">
		<!-- <common-header></common-header> -->
		<common-header v-on:childToParent="onChildClick"></common-header>
		<div id="logo">
			<div class="center">
				<div class="nake l">
					<p class="Gongpin l">
						<router-link to="home"><img :src="URL + logoPhoto" /></router-link>
					</p>
					<!-- <p class="iphone l">
						<span class="hot l">招商热线</span>
						<span class="shuzi l">{{$constant.tel}}</span>
					</p> -->
				</div>
				<div class="gold r">
					<img src="../../../assets/img/2.png" />
					<ul>
						<li>开店申请</li>
						<li>网门审核</li>
						<li>支付开店款项</li>
						<li>创建店铺</li>
					</ul>
				</div>
			</div>
		</div>
		<div class="middle">
			<div class="center">
				<div class="Box">
					<img src="../../../assets/img/deng.png" /><span class="spa">注意事项</span> <span>结算帐户信息为打款信息，请认真填写！</span>
				</div>
				<div class="show">
					<div class="Top">
						<h1>开户银行信息</h1>
						<p class="four"><span class="span">*</span>开户名 :
							<el-input size="small" @change="changeWarning(1)" v-model="account_name"></el-input>
							<el-alert v-if="tip.tip1" title="请输入正确的开户名" type="error" show-icon :closable="false"></el-alert>
						</p>
						<p class="six"><span class="span">*</span>银行账号 :
							<el-input size="small" @change="changeWarning(2)" v-model="company_account"></el-input>
							<el-alert v-if="tip.tip2" title="请输入正确的银行账号" type="error" show-icon :closable="false"></el-alert>
						</p>
						<p class="eight"><span class="span">*</span>开户银行支行名称 :
							<el-input size="small" @change="changeWarning(3)" v-model="branch_bank"></el-input>
							<el-alert v-if="tip.tip3" title="请输入支行名称" type="error" show-icon :closable="false"></el-alert>
						</p>
					</div>
					<div class="Top">
						<h1>结算账号信息</h1>
						<p class="four"><span class="span">*</span>开户名 :
							<el-input size="small" @change="changeWarning(4)" v-model="settle_name"></el-input>
							<el-alert v-if="tip.tip4" title="请输入正确的开户名" type="error" show-icon :closable="false"></el-alert>
						</p>
						<p class="six"><span class="span">*</span>银行账号 :
							<el-input size="small" @change="changeWarning(5)" v-model="settle_account"></el-input>
							<el-alert v-if="tip.tip5" title="请输入正确的银行账号" type="error" show-icon :closable="false"></el-alert>
						</p>
						<p class="eight"><span class="span">*</span>开户银行支行名称 :
							<el-input size="small" @change="changeWarning(6)" v-model="settle_bank"></el-input>
							<el-alert v-if="tip.tip6" title="请输入支行名称" type="error" show-icon :closable="false"></el-alert>
						</p>
					</div>
					<div class="Top">
						<h1>打款账号</h1>
						<p class="four"><span class="span">*</span>微信 :
							<el-input size="small" @change="changeWarning(7)" v-model="wx_account"></el-input>
							<el-alert v-if="tip.tip7" title="请输入微信账号" type="error" show-icon :closable="false"></el-alert>
						</p>
						<p class="six"><span class="span">*</span>支付宝 :
							<el-input size="small" @change="changeWarning(8)" v-model="alipay_account"></el-input>
							<el-alert v-if="tip.tip8" title="请输入支付宝账号" type="error" show-icon :closable="false"></el-alert>
						</p>
					</div>
					<div class="Bottom">
						<h1>税务登记证</h1>
						<p class="six"><span class="span">*</span>税务登记账号 :
							<el-input size="small" @change="changeWarning(9)" v-model="certificate_number"></el-input>
							<el-alert v-if="tip.tip9" title="请输入税务登记账号" type="error" show-icon :closable="false"></el-alert>
						</p>
						<div class="btu clearfix"><span class="span l">* <span class="tit">税务登记证号电子版 :</span></span>
							<span class="add-image l">
											<input v-if="uploadShow" @change="addImg($event)" type="file"  class="btn_file">
											<img @click="del" v-else :src="URL + taxpayer_img"/>
											<div class="load-animation" v-show="load">上传中<i class="el-icon-loading"></i></div>
										</span>
							<el-alert v-if="tip.tip10" title="请上传税务登记证号电子版 " type="error" show-icon :closable="false"></el-alert>
						</div>
						<button class="btn" @click="next">下一步</button>
					</div>
				</div>
			</div>
		</div>
	  <com-foot></com-foot> 
	</div>
</template>

<script>
import ComFoot from '@/common/footerDetail.vue';
	export default {
         components:{
            ComFoot
        },
		data() {
			return {
				store_id: '', //店铺编号id
				settle_name: '', //结算账户开户名
				account_name: '', //开户名
				company_account: '', //公司银行账号
				branch_bank: '', //开户银行支行名称
				settle_account: '', //算公司银行账号
				settle_bank: '', //结算开户银行支行名称
				certificate_number: '', //税务登记证号
				wx_account: '',
				alipay_account: '',
				load: false,
				uploadShow: true,
				taxpayer_img: '',
				tip: {
					tip1: false,
					tip2: false,
					tip3: false,
					tip4: false,
					tip5: false,
					tip6: false,
					tip7: false,
					tip8: false,
					tip9: false,
					tip10: false
				},
				reg: /^[1-9]\d*$/,
				name: /^[\u4E00-\u9FA5\uf900-\ufa2d·s]{2,20}$/,
				imgData: {
					accept: 'image/gif, image/jpeg, image/png, image/jpg',
				},
				fromChild: '',
				logoPhoto:'',
				headParams: {
					title: sessionStorage.getItem('titleKey'),
					description: sessionStorage.getItem('updateDescription'),
					keywords: sessionStorage.getItem('contentKey'),
					link:sessionStorage.getItem('pcfavicon')         
				}
			}
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
		watch: {
			taxpayer_img() {
				if (this.settle_name) {
					this.$set(this.tip, 'tip10', false);
				}
			}
		},
		mounted() {
			this.store_id = this.$route.params.id;			
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
			changeWarning(sign) {
				switch (sign) {
					case 1:
						if (this.name.test(this.account_name)) {
							this.$set(this.tip, 'tip1', false);
						}
						break;
					case 2:
						if (this.company_account.length > 12 && this.reg.test(this.company_account)) {
							this.$set(this.tip, 'tip2', false);
						}
						break;
					case 3:
						if (this.name.test(this.branch_bank)) {
							this.$set(this.tip, 'tip3', false);
						}
						break;
					case 4:
						if (this.name.test(this.settle_name)) {
							this.$set(this.tip, 'tip4', false);
						}
						break;
					case 5:
						if (this.settle_account.length > 12 && this.reg.test(this.settle_account)) {
							this.$set(this.tip, 'tip5', false);
						}
						break;
					case 6:
						if (this.name.test(this.settle_bank)) {
							this.$set(this.tip, 'tip6', false);
						}
						break;
					case 7:
						if (this.wx_account) {
							this.$set(this.tip, 'tip7', false);
						}
						break;
					case 8:
						if (this.alipay_account) {
							this.$set(this.tip, 'tip8', false);
						}
						break;
					case 9:
						if (this.certificate_number) {
							this.$set(this.tip, 'tip9', false);
						}
						break;
				}
			},
			del() {
				this.HTTP(this.$httpConfig.delPic, {
					fileName: this.taxpayer_img
				}, 'post').then((res) => {
					this.uploadShow = true;
					this.taxpayer_img = '';
				}).catch((res) => {
					alert(res.data.message)
				})
			},
			addImg(e) {
				let that = this;   
				let file = e.target.files[0];
				let type = file.type;
				if (this.imgData.accept.indexOf(type) == -1) {
					this.$set(this.tip, 'tip10', true);
					return false;
				}
				this.load = true;
				let  form  =  new  FormData();
				form.append('adv_content', file, file.name);  
				let config = {
					headers: {
						"Content-Type": "multipart/form-data"
					}
				};   
				this.$ajax.post(this.$httpConfig.upLoadImage, form, config)
					.then(res => {
						if (res.data.status == 10001) {
							this.$router.push("/passwordLogin");
						} else {
							if (res.data.status === 1) {
								that.taxpayer_img = res.data.data;
								that.uploadShow = false;
								that.load = false;
							} else {
								alert(res.data.message);
								that.load = false;
							}
						}
					})
			},
			next() {
				// this.$router.push({
				// 		name: 'companyShopInfo',
				// 		// params: {
				// 		// 	id: res.data.data
				// 		// }
				// 	});
				if (!this.name.test(this.account_name)) {
					this.$set(this.tip, 'tip1', true);
				}
				if (this.company_account.length < 12 || this.reg.test(this.company_account) === false) {
					this.$set(this.tip, 'tip2', true);
				}
				if (!this.name.test(this.branch_bank)) {
					this.$set(this.tip, 'tip3', true);
				}
				if (!this.name.test(this.settle_name)) {
					this.$set(this.tip, 'tip4', true);
				}
				if (this.settle_account.length < 12 || !this.reg.test(this.settle_account)) {
					this.$set(this.tip, 'tip5', true);
				}
				if (!this.name.test(this.settle_bank)) {
					this.$set(this.tip, 'tip6', true);
				}
				if (!this.certificate_number) {
					this.$set(this.tip, 'tip7', true);
				}
				if (!this.wx_account) {
					this.$set(this.tip, 'tip8', true);
				}
				if (!this.alipay_account) {
					this.$set(this.tip, 'tip9', true);
				}
				if (!this.taxpayer_img) {
					this.$set(this.tip, 'tip10', true);
				}
				for (const key in this.tip) {
					if (this.tip[key] == true) {
						return;
					}
				}
				var params = {
					// store_id: this.store_id, //店铺编号id
					settle_name: this.settle_name, //结算账户开户名
					account_name: this.account_name, //开户名
					company_account: this.company_account, //公司银行账号
					branch_bank: this.branch_bank, //开户银行支行名称
					settle_account: this.settle_account, //算公司银行账号
					settle_bank: this.settle_bank, //结算开户银行支行名称
					certificate_number: this.certificate_number, //税务登记证号
					registration_electronic: this.taxpayer_img,
					alipay_account: this.alipay_account,
					wx_account: this.wx_account
				}
				this.HTTP(this.$httpConfig.storeBank, params, 'post').then((res) => {
					this.$router.push({
						name: 'companyShopInfo',
						// params: {
						// 	id: res.data.data
						// }
					});
				}).catch(res => {
					alert(res.data.message)
				})
			}
		}
	
	}
</script>

<style lang='less' scoped>
	.el-alert {
		display: inline;
		margin-left: 15px;
		padding: 0;
	}
	
	.el-alert--error {
		background-color: transparent;
	}
	
	.el-input {
		width: 286px;
		margin-left: 31px;
	}
	
	.center {
		width: 1200px;
		height: 100%;
		margin: 0 auto;
	}
	
	.l {
		float: left;
	}
	
	.r {
		float: right;
	}
	
	#logo {
		width: 100%;
		height: 104px;
		.center {
			height: 104px;
			.Gongpin {
				height: 104px;
				img {
					margin-top: 20px;
					padding-right: 20px;
					height: 60px;
					width: 184px;
				}
			}
			.iphone {
				width: 240px;
				height: 104px;
				.hot {
					display: inline-block;
					width: 70px;
					height: 104px;
					line-height: 104px;
					font-size: 13px;
					margin-left: 20px;
				}
				.shuzi {
					display: inline-block;
					height: 104px;
					line-height: 104px;
					font-size: 16px;
					color: #d53738;
					font-weight: 500;
				}
			}
			.gold {
				margin-top: 34px;
				margin-left: 162px;
				ul {
					overflow: hidden;
					margin-top: 7px;
					li {
						float: left;
						font-size: 12px;
						color: #666;
					}
					li:nth-of-type(1) {
						margin: 0 72px 0 35px;
				
					}
					li:nth-of-type(2) {
						color: #d02629;
					}
						li:nth-of-type(3) {
				    position: relative;
    				left: 54px;
					}
						li:nth-of-type(4) {
					position: relative;
    			left: 110px;
					}
				}
			}
		}
	}
	
	.middle {
		background: #fafafa;
		padding: 20px 0;
		#btn_file {
			border: 0;
			margin-left: 25px;
			width: 160px;
			margin-top: 4px;
		}
		.center {
			background: #fff;
			overflow: hidden;
			.Box {
				height: 97px;
				border: 1px solid #bce8f1;
				background: #eff8ff;
				margin: 51px 110px 33px;
				img {
					margin: 27px 16px 0 40px;
					float: left;
				}
				span {
					font-size: 12px;
					color: #b1b4b6;
					float: left;
					width: 70%;
				}
				.spa {
					color: #333;
					font-size: 14px;
					margin: 25px 0 3px;
				}
			}
			input {
				width: 286px;
				height: 30px;
				border: 1px solid #ccc;
				margin-left: 31px;
			}
			select {
				margin-left: 31px;
				width: 286px;
				height: 30px;
				border: 1px solid #ccc;
				outline: none;
				color: #888;
			}
			.span {
				color: #ff3f3f;
				margin-right: 5px;
			}
			.show {
				margin: 0 110px;
				input {
					padding-left: 5px;
				}
				h1 {
					font-size: 14px;
					color: #333;
					margin-bottom: 22px;
				}
				p {
					font-size: 12px;
					color: #555;
					margin-bottom: 20px;
					line-height: 32px;
				}
				p.four {
					margin-left: 142px;
				}
				p.eight {
					margin-left: 83px;
				}
				p.six {
					margin-left: 94px;
				}
				.Top {
					height: 211px;
					border-bottom: 1px dashed #d7d7d7;
					margin-bottom: 31px;
					p.six {
						margin-left: 130px;
					}
				}
				.Bottom {
					/*height: 330px;*/
                    height: 450px;
					font-size: 12px;
					color: #555;
					p.six {
						margin-left: 109px;
					}
					.btu {
						margin-left: 76px;
						margin-bottom: 58px;
						button {
							cursor: pointer;
							width: 74px;
							height: 21px;
							text-align: center;
							line-height: 21px;
							border: 1px solid #959595;
							margin: 0 22px;
							font-size: 12px;
							color: #060606;
						}
						.tit {
							color: #555;
						}
					}
					.add-image {
						position: relative;
						background: url(../../../assets/img/shangchuan.png) no-repeat 50% 50%;
						border: 1px dashed #ccc;
						margin: 0 42px 0 31px;
						width: 163px;
						height: 122px;
						input {
							padding: 0;
							margin: 0;
							width: 100%!important;
							height: 100%;
							opacity: 0;
						}
						img {
							display: block;
							width: 100%;
							height: 100%;
							background-color: #fff;
						}
						.load-animation {
							position: absolute;
							top: 0;
							left: 0;
							bottom: 0;
							right: 0;
							background: #060606;
							text-align: center;
							padding: 45px 0;
							opacity: .4;
							font-size: 14px;
							color: #fff;
						}
					}
					.btn {
						cursor: pointer;
						width: 94px;
						height: 32px;
						border-radius: 4px;
						text-align: center;
						line-height: 32px;
						color: #fff;
						font-size: 12px;
						background: #d02629;
						margin-left: 224px;
					}
				}
			}
		}
	}
	
	#commonFooter {
		width: 100%;
		height: 90px;
		.center {
			height: 90px;
			ul {
				width: 100%;
				height: 12px;
				line-height: 12px;
				margin-top: 20px;
				margin-left: 470px;
				li {
					width: 60px;
					height: 12px;
					border-right: 1px solid #8b8b8b;
					font-size: 12px;
					text-align: center;
				}
				li:last-child {
					border-right: 0;
				}
			}
			.online {
				width: 100%;
				height: 26px;
				line-height: 26px;
				text-align: center;
				font-size: 12px;
				margin-top: 5px;
			}
			.onlines {
				width: 100%;
				height: 26px;
				line-height: 26px;
				text-align: center;
				font-size: 12px;
			}
		}
	}
</style>
