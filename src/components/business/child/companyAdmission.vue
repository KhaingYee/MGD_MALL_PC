<template>
	<div id="interview">
		<!-- <common-header></common-header> -->
		<common-header v-on:childToParent="onChildClick"></common-header>
		<div id="logo">
			<div class="center">
				<div class="nake l">
					<p class="Gongpin l"><router-link to="home"><img :src="URL + logoPhoto" /></router-link></p>
					<!-- <p class="iphone l">
						<span class="hot l">招商热线</span>
						<span class="shuzi l">{{$constant.tel}}</span>
					</p> -->
				</div>
				<div class="gold r">
					<img src="../../../assets/img/1.png" />
					<ul>
						<li>企业开店申请</li>
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
					<img src="../../../assets/img/deng.png" /><span class="spa">注意事项</span> <span>以下所需要上传的电子版资质文件仅支持JPG\GIF\PNG格式图片，大小请控制在1M之内。</span>
				</div>
				<div class="show">
					<div class="top">
						<h1>企业信息 <span>（按照证书上的内容逐字填写）</span></h1>
						<p class="four"><span class="span">*</span>店铺名称 :
							<el-input size="small" @change="changeWarning(15)" v-model="store_name"></el-input>
							<el-alert v-if="tip.tip15" title="请填写店铺名称" type="error" show-icon :closable="false"></el-alert>
						</p>
						<p class="four"><span class="span">*</span>公司名称 :
							<el-input size="small" @change="changeWarning(1)" v-model="company_name"></el-input>
							<el-alert v-if="tip.tip1" title="请填写公司名称" type="error" show-icon :closable="false"></el-alert>
						</p>
						<p class="five"><span class="span">*</span>公司所在地 :<select class="behind address" v-model="selectProvId" @change="getCityArea(selectProvId,'city','c')">
												<option v-if="selectProvId == '请选择'" value="请选择">请选择</option>
												<option :value="item.id" v-for="(item,index) in provList" :key="index">
													{{ item.name }}
												</option>
											</select>
							<select class="behind" v-model="selectCityId" @change="getCityArea(selectCityId,'area','t')">
												<option v-if="selectCityId == '请选择'" value="请选择">请选择</option>
												<option :value="item.id" v-for="(item,index) in cityList" :key="index">{{item.name}}</option>
											</select>
							<select class="behind" v-model="selectAreaId" @change="changeWarning(2)">
												<option v-if="selectAreaId=='请选择'" value="请选择">请选择</option>
												<option :value="item.id" v-for="(item,index) in areaList" :key="index">{{item.name}}</option>
											</select>
							<el-alert v-if="tip.tip2" title="请填完善地址" type="error" show-icon :closable="false"></el-alert>
						</p>
						<p class="six"><span class="span">*</span>公司详细地址 :
							<el-input size="small" @change="changeWarning(3)" v-model="address"></el-input>
							<el-alert v-if="tip.tip3" title="请填完善详细地址" type="error" show-icon :closable="false"></el-alert>
						</p>
						<p class="four"><span class="span">*</span>公司电话 :
							<el-input size="small" @change="changeWarning(4)" v-model="company_mobile"></el-input>
							<el-alert v-if="tip.tip4" title="请输入正确的公司电话" type="error" show-icon :closable="false"></el-alert>
						</p>
						<p class="four"><span class="span">*</span>注册资金 :
							<el-input size="small" @change="changeWarning(5)" v-model="registered_capital"></el-input>
							<el-alert v-if="tip.tip5" title="注册资金错误" type="error" show-icon :closable="false"></el-alert>
						</p>
						<p class="five"><span class="span">*</span>联系人姓名 :
							<el-input size="small" @change="changeWarning(6)" v-model="name"></el-input>
							<el-alert v-if="tip.tip6" title="请输入联系人姓名" type="error" show-icon :closable="false"></el-alert>
						</p>
						<p class="five"><span class="span">*</span>联系人手机 :
							<el-input size="small" @change="changeWarning(7)" v-model="mobile"></el-input>
							<el-alert v-if="tip.tip7" title="请输入正确的手机号码" type="error" show-icon :closable="false"></el-alert>
						</p>
					</div>
					<div class="bottom">
						<h1>营业执照信息（副本）</h1>
						<p class="five"><span class="span">*</span>营业执照号 :
							<el-input size="small" @change="changeWarning(8)" v-model="license_number"></el-input>
							<el-alert v-if="tip.tip8" title="请输入营业执照号" type="error" show-icon :closable="false"></el-alert>
						</p>
						<p class="seven"><span class="span">*</span>营业执照有效期 :
							<el-date-picker class="start_time" v-model="start_time" type="date" value-format="yyyy-MM-dd" placeholder="选择日期"></el-date-picker>
							—
							<el-date-picker v-model="end_time" type="date" value-format="yyyy-MM-dd" placeholder="选择日期"></el-date-picker>
							<el-alert v-if="tip.tip9" title="请完善有效期" type="error" show-icon :closable="false"></el-alert>
						</p>
						<div class="six">
							<p class="l"><span class="span">*</span>法定经营范围 :</p><textarea @change="changeWarning(10)" v-model="scope_of_operation" class="l"></textarea>
							<el-alert v-if="tip.tip10" title="请输入法定经营范围" type="error" show-icon :closable="false"></el-alert>
						</div>
						<p class="seven clearfix">
							<span class="electronic l">
												<i>* </i>营业执照电子版 :
											</span>
							<span class="add-image l">
												<input v-if="uploadShow" @change="addImg($event,1)" type="file"  class="btn_file">
												<img @click="del(0)" v-else :src="URL + license_img"/>
												<div class="load-animation" v-show="load1">上传中<i class="el-icon-loading"></i></div>
											</span>
							<span class="huise">请确保图片清晰，文字可辩并有清晰的红色公章</span>
							<el-alert v-if="tip.tip11" title="请上传营业执照" type="error" show-icon :closable="false"></el-alert>
						</p>
					</div>
					<div class="down">
						<h1>组织机构代码<span>（企业三证合一的没有组织机构代码的上传营业执照）</span></h1>
						<p class="six"><span class="span">*</span>组织机构代码 :
							<el-input size="small" @change="changeWarning(12)" v-model="organization_code"></el-input>
							<el-alert v-if="tip.tip12" title="请输入组织机构代码" type="error" show-icon :closable="false"></el-alert>
						</p>
						<p class="seven clearfix">
							<span class="electronic l">
												<i>* </i>组织机构代码证电子版 :
											</span>
							<span class="add-image l">
												<input v-if="uploadShow1" @change="addImg($event,2)" type="file"  class="btn_file">
												<img @click="del(1)" v-else :src="URL + organization_img"/>
												<div class="load-animation" v-show="load2">上传中<i class="el-icon-loading"></i></div>
											</span>
							<span class="huise">请确保图片清晰，文字可辩并有清晰的红色公章</span>
							<el-alert v-if="tip.tip13" title="请上传组织机构代码证" type="error" show-icon :closable="false"></el-alert>
						</p>
					</div>
					<div class="base">
						<h1>一般纳税人证明 <span>注：所属企业具有一般纳税人证明时，此项为必填。</span></h1>
						<p class="seven clearfix">
							<span class="electronic l">
												<i>* </i>一般纳税人证明 :
											</span>
							<span class="add-image l">
												<input v-if="uploadShow2" @change="addImg($event,3)" type="file"  class="btn_file">
												<img @click="del(2)" v-else :src="URL + taxpayer_img"/>
												<div class="load-animation" v-show="load3">上传中<i class="el-icon-loading"></i></div>
											</span>
							<span class="huise">请确保图片清晰，文字可辩并有清晰的红色公章</span>
							<el-alert v-if="tip.tip14" title="请上传组织机构代码证" type="error" show-icon :closable="false"></el-alert>
						</p>
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
				uploadShow: true,
				uploadShow1: true,
				uploadShow2: true,
				license_img: '',
				organization_img: '',
				taxpayer_img: '',
				store_name: '',
				company_name: '',
				address: '',
				company_mobile: '',
				registered_capital: '',
				name: '',
				mobile: '',
				license_number: '',
				start_time: null,
				end_time: null,
				scope_of_operation: '',
				electronic_version: '',
				organization_code: '',
				organization_electronic: '',
				taxpayer_certificate: '',
				provList: [], //省级列表
				selectProvId: '请选择',
				cityList: [], //市列表
				selectCityId: '请选择',
				areaList: [], //区列表
				selectAreaId: '请选择',
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
					tip10: false,
					tip11: false,
					tip12: false,
					tip13: false,
					tip14: false,
					tip15: false
				},
				load1: false,
				load2: false,
				load3: false,
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
			this.getProvince();
			this.getFootData();
      		this.getFavIcon(); 
		},
		mounted(){
			var self = this
				window.setTimeout(function () {
					self.headParams.title = sessionStorage.titleKey
					self.headParams.description = sessionStorage.updateDescription
					self.headParams.keywords = sessionStorage.contentKey
					self.headParams.link = sessionStorage.pcfavicon
					self.$emit('updateHead')
			}, 3000)
		},
		watch: {
			start_time() {
				if (this.start_time != null && this.end_time != null) {
					this.$set(this.tip, 'tip9', false);
				}
			},
			end_time() {
				if (this.start_time != null && this.end_time != null) {
					this.$set(this.tip, 'tip9', false);
				}
			},
			license_img() {
				if (this.license_img != '') {
					this.$set(this.tip, 'tip11', false);
				}
			},
			organization_img() {
				if (this.organization_img != '') {
					this.$set(this.tip, 'tip13', false);
				}
			},
			taxpayer_img() {
				if (this.taxpayer_img != '') {
					this.$set(this.tip, 'tip14', false);
				}
			}
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
			del(mark) {
				let image = '';
				if(mark === 0){
					image = this.license_img;
				}else if(mark === 1){
					image = this.organization_img;
				}else{
					image = this.URL + this.taxpayer_img;
				}
				this.HTTP(this.$httpConfig.delPic, {
					fileName: image
				}, 'post').then((res) => {
					if (mark == 0) {
						this.uploadShow = true;
						this.license_img = '';
					} else if (mark == 1) {
						this.uploadShow1 = true;
						this.organization_img = '';
					} else {
						this.uploadShow2 = true;
						this.taxpayer_img = '';
					}
				}).catch((res) => {
					alert(res.data.message)
				})
	
			},
			addImg(e, s) {
				let that = this;   
				let file = e.target.files[0];
				let type = file.type;
				if (this.imgData.accept.indexOf(type) == -1) {
					if (s == 1) {
						this.$set(this.tip, 'tip11', true);
					} else if (s == 2) {
						this.$set(this.tip, 'tip13', true);
					} else {
						this.$set(this.tip, 'tip14', true);
					}
					return false;
				}
				if (s == 1) {
					this.load1 = true;
				} else if (s == 2) {
					this.load2 = true;
				} else {
					this.load3 = true;
				}
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
								if (s == 1) {
									that.license_img = res.data.data;
									that.uploadShow = false;
									that.load1 = false;
								} else if (s == 2) {
									that.organization_img = res.data.data;
									that.uploadShow1 = false;
									that.load2 = false;
								} else {
									that.taxpayer_img = res.data.data;
									that.uploadShow2 = false;
									that.load3 = false;
								}
							} else {
								alert(res.data.message);
								if (s == 1) {
									that.load1 = false;
								} else if (s == 2) {
									that.load2 = false;
								} else {
									that.load3 = false;
								}
							}
						}
					})
			},
			changeWarning(sign) {
				switch (sign) {
					case 1:
						if (this.company_name != '') {
							this.$set(this.tip, 'tip1', false);
						}
						break;
					case 2:
						if (this.selectCityId != '请选择' && this.selectAreaId != '请选择') {
							this.$set(this.tip, 'tip2', false);
						}
						break;
					case 3:
						if (this.address != '') {
							this.$set(this.tip, 'tip3', false);
						}
						break;
					case 4:
						if ((/^((0\d{2,3}-\d{7,8})|(1[345789]\d{9}))$/.test(this.company_mobile))) {
							this.$set(this.tip, 'tip4', false);
						}
						break;
					case 5:
						if ((/(^[1-9]([0-9]+)?(\.[0-9]{1,2})?$)|(^(0){1}$)|(^[0-9]\.[0-9]([0-9])?$)/.test(this.registered_capital))) {
							this.$set(this.tip, 'tip5', false);
						}
						break;
					case 6:
						if (this.name != '') {
							this.$set(this.tip, 'tip6', false);
						}
						break;
					case 7:
						if ((/^1[345789]\d{9}$/.test(this.mobile))) {
							this.$set(this.tip, 'tip7', false);
						}
						break;
					case 8:
						if (this.license_number != '') {
							this.$set(this.tip, 'tip8', false);
						}
						break;
					case 10:
						if (this.scope_of_operation != '') {
							this.$set(this.tip, 'tip10', false);
						}
						break;
					case 12:
						if (this.organization_code != '') {
							this.$set(this.tip, 'tip12', false);
						}
						break;
					case 15:
						if (this.organization_code != '') {
							this.$set(this.tip, 'tip15', false);
						}
						break;
				}
			},
			next() {
				// this.$router.push({
				// 	name:'companyBankInfo',
				// 	params:{
				// 		id:1
				// 	}
				// });
				if (this.store_name == '') {
					this.$set(this.tip, 'tip15', true);
				}
				if (this.company_name == '') {
					this.$set(this.tip, 'tip1', true);
				}
				if (this.selectCityId == '请选择' || this.selectAreaId == '请选择') {
					this.$set(this.tip, 'tip2', true);
				}
				if (this.address == '') {
					this.$set(this.tip, 'tip3', true);
				}
				if (!(/^((0\d{2,3}-?)?\d{7,8}|(1[3584]\d{9}))$/.test(this.company_mobile))) {
					this.$set(this.tip, 'tip4', true);
				}
				if (!(/(^[1-9]([0-9]+)?(\.[0-9]{1,2})?$)|(^(0){1}$)|(^[0-9]\.[0-9]([0-9])?$)/.test(this.registered_capital))) {
					this.$set(this.tip, 'tip5', true);
				}
				if (this.name == '') {
					this.$set(this.tip, 'tip6', true);
				}
				if (!(/^1[345789]\d{9}$/.test(this.mobile))) {
					this.$set(this.tip, 'tip7', true);
				}
				if (this.license_number == '') {
					this.$set(this.tip, 'tip8', true);
				}
				if (this.start_time == null || this.end_time == null) {
					this.$set(this.tip, 'tip9', true);
				}
				if (this.scope_of_operation == '') {
					this.$set(this.tip, 'tip10', true);
				}
				if (this.license_img == '') {
					this.$set(this.tip, 'tip11', true);
				}
				if (this.organization_code == '') {
					this.$set(this.tip, 'tip12', true);
				}
				if (this.organization_img == '') {
					this.$set(this.tip, 'tip13', true);
				}
				if (this.taxpayer_img == '') {
					this.$set(this.tip, 'tip14', true);
				}
				for (const key in this.tip) {
					if (this.tip[key] == true) {
						return;
					}
				}
				var params = {
					store_name: this.store_name,
					company_name: this.company_name,
					prov_id: this.selectProvId,
					city: this.selectCityId,
					dist: this.selectAreaId,
					address: this.address,
					company_mobile: this.company_mobile,
					registered_capital: this.registered_capital,
					name: this.name,
					mobile: this.mobile,
					electronic_version: this.license_img,
					license_number: this.license_number,
					validity_start: this.start_time,
					validity_end: this.end_time,
					scope_of_operation: this.scope_of_operation,
					organization_code: this.organization_code,
					organization_electronic: this.organization_img,
					taxpayer_certificate: this.taxpayer_img
				};
				this.HTTP(this.$httpConfig.StoreJoinCompany, params, 'post').then((res) => {
					this.$router.push({
						name: 'companyBankInfo',
						params: {
							id: res.data.data
						}
					});
				}).catch((res) => {
					alert(res.data.message)
				})
			},
			//获取省
			getProvince() {
				this.HTTP(this.$httpConfig.regionLists, {}, 'get')
					.then((res) => {
						this.provList = res.data.data;
					})
			},
			//获取市区
			getCityArea(id, name, status) {
				this.HTTP(this.$httpConfig.regionLists, {
						id: id
					}, 'get')
					.then((res) => {
						if (status == 'c') {
							this.selectCityId = '请选择';
							this.selectAreaId = '请选择';
							this.areaList = [];
						} else if (status == 't') {
							this.selectAreaId = '请选择';
						}
						if (name == 'city') {
							this.cityList = res.data.data;
						} else if (name == 'area') {
							this.areaList = res.data.data;
						}
	
					})
			},
	
		}
	
	}
</script>

<style lang='less' scoped>
	.el-input {
		width: 286px;
		margin-left: 31px;
	}
	
	.center {
		width: 1200px;
		height: 100%;
		margin: 0 auto;
		padding: 0 0 40px 0;
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
					    margin: 0 47px 0 35px;
    					color: #d02629;
					}
					li:nth-of-type(3) {
						margin: 0 56px 0 56px;
					}
				}
			}
		}
	}
	
	.middle {
		background: #fafafa;
		padding: 20px 0;
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
				padding-left: 8px;
				width: 286px;
				height: 30px;
				border: 1px solid #ccc;
				margin-left: 31px;
			}
			select {
				margin-left: 31px;
				height: 30px;
				border: 1px solid #ccc;
				outline: none;
				color: #888;
			}
			.span {
				color: #ff3f3f;
				margin-right: 5px;
			}
			.electronic {
				i {
					color: #ff3f3f;
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
			}
			.show {
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
				margin: 0 110px;
				.el-alert {
					display: inline;
					margin-left: 15px;
					padding: 0;
				}
				.el-alert--error {
					background-color: transparent;
				}
				h1 {
					font-size: 14px;
					color: #333;
					margin-bottom: 22px;
					span {
						color: #999;
						font-size: 13px;
					}
				}
				p {
					font-size: 12px;
					color: #555;
					margin-bottom: 20px;
					line-height: 32px;
				}
				p.four {
					margin-left: 118px;
				}
				p.three {
					margin-left: 131px;
				}
				p.five {
					margin-left: 107px;
					.address {
						margin-left: 31px!important;
					}
					.behind {
						margin-left: 10px;
						min-width: 100px!important;
					}
				}
				p.two {
					margin-left: 142px;
				}
				p.six {
					margin-left: 94px;
				}
				.top {
					border-bottom: 1px dashed #d7d7d7;
					margin-bottom: 40px;
				}
				.bottom {
					border-bottom: 1px dashed #d7d7d7;
					p.seven {
						margin-left: 84px;
						input {
							width: 147px;
						}
						input:nth-of-type(2) {
							margin-left: 0;
						}
						.start_time {
							margin-left: 26px;
						}
						.el-date-editor.el-input {
							width: 148px;
						}
					}
					.six {
						overflow: hidden;
						margin-left: 97px;
						margin-bottom: 20px;
						textarea {
							padding: 8px 0 0 8px;
							width: 286px;
							height: 75px;
							margin-left: 31px;
						}
					}
					.huise {
						color: #999;
					}
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
				}
				.down {
					border-bottom: 1px dashed #d7d7d7;
					h1 {
						margin-top: 31px;
						span {
							color: #999;
							font-size: 13px;
						}
					}
					p.seven {
						margin-left: 46px;
						.huise {
							color: #999;
						}
					}
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
				}
				.base {
                    height: 360px;
					h1 {
						margin-top: 31px;
						span {
							color: #999;
							font-size: 13px;
						}
					}
					p {
						margin: 23px 0 69px 84px;
						.huise {
							color: #999;
						}
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
						margin-left: 205px;
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
