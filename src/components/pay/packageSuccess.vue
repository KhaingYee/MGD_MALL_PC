<template>
	<div class="payment">
		<div class="pay">
			<div class="center">
				<div class="header">
					<router-link to="/home">
						<img class="l" :src="URL + logoPhoto"/>
					</router-link>
					<p class="l">我的收银台</p>
				</div>
				<div class="payment-info">
					<div class="success-item">
						<div class="payment-success">
							<i class="success-icon"></i>
							<p class="success-tit">您已成功付款</p>
						</div>
						<div class="return-look">返回个人中心 <router-link to="/packageOrder">查看</router-link> </div>
					</div>
				</div>
			</div>
		</div>
		<foot-com></foot-com>
	</div>
</template>
<script>
export default {
	data() {
		return {
			logoPhoto:''
		}
	},
	created(){
		this.getFootData();
	},
	methods:{
		getFootData() {
			this.HTTP(this.$httpConfig.aboutEtcetera, {}, "post")
				.then(res => {
					this.logoPhoto = res.data.data.logo_name;
				})
				.catch(err => {
					console.log(err);
				});
		},
	},
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
		background: #fff;
	}
	.pay {
		background: #fff;
		.header {
			height: 109px;
			border-bottom: 1px solid #f6f5f5;
			img {
				margin: 31px 0 0 20px;
				width: 184px;
				height: 53px;
			}
			p {
				line-height: 30px;
				border-left: 1px solid #cdcfd0;
				font-size: 18px;
				color: #555;
				margin: 45px 0 0 20px;
				padding-left: 17px;
			}
		}
		.payment-info{
			background-color:#ffffeb;
			border: 1px solid #f8e1ad;
			margin: 25px 0 40px 0;;
			.success-item{
				position: relative;
				box-sizing: content-box;
				width: 100px;
				margin: 0 auto;
				padding: 50px 0;
				.payment-success{
					.success-icon{
						top: 40px;
    				left: -60px;
						float: left;
						position: absolute;
						width: 45px;
						height: 45px;
						background: url(../../assets/img/success-icon.png) no-repeat;
						background-size: 100% 100%;
					}
					.success-tit{
						font-size: 16px;
					}
				}
				.return-look{
					margin-top: 4px;
					font-size: 12px;
				}
			}
		}
	}
</style>