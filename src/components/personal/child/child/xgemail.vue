<template>
	<div class="only">
		<ul class="ul">
			<li>修改邮箱验证</li>
		</ul>
		<img v-show="imgs" class="logo" src="../../../../assets/img/embuzhou1.png" />
		<img v-show="imgss" class="logo" src="../../../../assets/img/embuzhou2.png" />
		<ul>
			<!-- <li class="l" :class="{colour:isok}">手机验证</li> -->
			<li :class="{colour:isoks}" class="l left">新的邮箱地址</li>
			<li :class="{colour:isokss}" class="l">完成</li>
		</ul>
		<!-- <div v-show="first">
			<p class="shouji">已绑定的手机：<span v-if="users.mobile">sdfsdf{{users.mobile | phoneEncryption}}</span></p>
			<p class="yanzhengma">
        <span>*</span>
        验证码：<input type="text" />
        <span></span>
        <img src="../../../../assets/img/yanzhengma1.png" />
      </p>
			<p class="dongtai">
        <span>*</span>
        <span>手机动态验证码：</span>
        <input class="l" type="text" />
        <span class="l" :class="{active:isActive}" @click="abtain">{{btnText}}</span>
      </p>
			<button @click="next">下一步</button>ßß
		</div> -->
		<div v-show="second">
			<p class="new newAddress">
        <span>*</span>
        新的邮箱地址：<input type="text" v-model="newAddress"/>
        <button class="verify" @click="getemail">发送验证码</button>
      </p>
			<p class="yanzhengma">
        <span>*</span>
        验证码：<input type="text" v-model="code"/>
        <span></span>
        <!-- <img src="../../../../assets/img/yanzhengma1.png" /> -->
      </p>
			<!-- <p class="dongtai">
        <span>*</span>
        <span>手机动态验证码：</span>
        <input class="l" type="text" />
        <span class="l" :class="{actives:isActives}" @click="abtains">{{btnText}}</span>
      </p> -->
			<button @click="last">下一步</button>
		</div>
		<div v-show="third" class="samll">
			<img class="duihao" src="../../../../assets/img/bigduihao.png" />
			<p class=" success">恭喜您！修改邮箱成功！</p>
			<!-- <p class="bangding">您绑定的邮箱是：<span>liaoxiao1990@163.com</span></p> -->
			<p class="goback" @click="goback">返回账户安全中心>></p>
		</div>
	</div>
</template>

<script>
import QS from "qs";
export default {
  data() {
    return {
      code:null,
      newAddress:null,
      //账户安全
      imgs: true,
      imgss: false,
      second: true,
      third: false,
      isoks: true,
      isokss: false,
      btnText: "获取验证码",
      isActive: "",
      mobile: "",
			isActives: "",
			users: []
    };
  },
  mounted() {
    this.userInfo()
  },
  methods: {
    //发送邮件
			getemail(){
				var reg = /^([0-9A-Za-z\-_\.]+)@([0-9a-z]+\.[a-z]{2,3}(\.[a-z]{2})?)$/g;
				if(reg.test(this.newAddress)){
						this.HTTP(this.$httpConfig.emailsendMailbox,{
						email:this.newAddress
					},'post').then(res=>{
						this.$message.info('已发送，验证码90秒后失效，请及时填写')
					}).catch(err=>{
						this.$message.error(err.data.message)
					})
				}else{
					this.$message.error('您的邮箱格式不正确，请重新输入');
					this.newAddress = null;
				}
			},
		userInfo () {
			this.HTTP(this.$httpConfig.userInfo, {}, 'post').then((res) => {
				this.users = res.data.data
			})
		},
    abtain() {
      if (this.isActive == true) return;
      var N = 60,
        _this = this,
        clear = null;
      _this.isActive = true;
      _this.btnText = "请" + N + "秒后重试";
      _this.isActive = true;
      clear = setInterval(function() {
        _this.btnText = "请" + N-- + "秒后重试";
        if (N < 0) {
          clearInterval(clear);
          _this.btnText = "获取验证码";
          _this.isActive = false;
        }
      }, 1000);
      this.HTTP(
        this.$httpConfig.sms,
        {
          mobile: this.mobile
        },
        "post"
      ).then(res => {
        this.$message({
          message: res.data.data.test_code,
          type: "success"
        });
        if (res.data.status == 1) {
          this.token = res.data.data.token;
        } else {
          clearInterval(clear);
          _this.btnText = "再次获取验证码";
          _this.isActive = false;
        }
      });
    },
    abtains() {
      if (this.isActives == true) return;
      var N = 60,
        _this = this,
        clear = null;
      _this.isActive = true;
      _this.btnText = "请" + N + "秒后重试";
      _this.isActive = true;
      clear = setInterval(function() {
        _this.btnText = "请" + N-- + "秒后重试";
        if (N < 0) {
          clearInterval(clear);
          _this.btnText = "获取验证码";
          _this.isActive = false;
        }
      }, 1000);
      this.HTTP(
        this.$httpConfig.sms,
        {
          mobile: this.mobile
        },
        "post"
      ).then(res => {
        this.$message({
          message: res.data.data.test_code,
          type: "success"
        });
        if (res.data.status == 1) {
          this.token = res.data.data.token;
        } else {
          clearInterval(clear);
          _this.btnText = "再次获取验证码";
          _this.isActive = false;
        }
      });
    },
    next() {
      (this.first = false), (this.second = true);
      (this.img = false), (this.imgs = true), (this.isoks = true);
    },
    last() {
      if(this.code!=null){
						this.HTTP(this.$httpConfig.emailverifMailbox,{
						email:this.newAddress,
						code:this.code
					},'post').then(res=>{
						this.$message.info(res.data.message);
						this.third = true;
            this.second = false;
            (this.imgs = false), (this.imgss = true), (this.isokss = true);
					}).catch(err=>{
						this.$message.error(err.data.message);
					})
				}else{
					this.$message.error('验证码不能为空')
			}
      
    },

    goback() {
      this.third = false;
      (this.first = true), (this.second = false);
      (this.img = true),
        (this.imgs = false),
        (this.imgss = false),
        (this.isok = true),
        (this.isoks = false),
        (this.isokss = false),
        this.$emit("Goback");
    }
  }
};
</script>

<style lang="less" scoped>
.verify{
		margin-left:10px!important;
	}
.left{
		margin-left:100px;
	}
.l {
  float: left;
}

.r {
  float: right;
}

.only {
  .ul {
    overflow: hidden;
    height: 37px;
    border-bottom: 1px solid #e7e7e7;
    margin: 14px 17px 0;
    li {
      width: 104px;
      text-align: center;
      line-height: 35px;
      border-bottom: 2px solid #d02629;
      color: #d02629;
    }
  }
  img.logo {
    margin: 59px 177px 0;
  }
  ul {
    overflow: hidden;
    margin: 13px 0 64px 79px;
    li {
      width: 241px;
      text-align: center;
      color: #ccc;
      font-size: 14px;
    }
  }
  .colour {
    color: #afd129;
  }
  p {
    font-size: 12px;
    color: #333;
  }
  p.shouji {
    margin: 25px 0 0 212px;
    span {
      color: #999;
    }
  }
  p.yanzhengma {
    margin: 25px 0 0 230px;
    span:nth-of-type(1) {
      color: #ff3f3f;
      margin-right: 13px;
    }
    input {
      width: 180px;
      height: 38px;
      border: 1px solid #ccc;
      border-radius: 3px;
      margin-right: 15px;
    }
  }
  p.new {
    margin: 53px 0 0 205px;
    span {
      color: #ff3f3f;
      margin-right: 13px;
    }
    input {
      width: 280px;
      height: 38px;
      border: 1px solid #ccc;
      border-radius: 3px;
    }
  }
  p.dongtai {
    margin: 21px 0 0 182px;
    overflow: hidden;
    line-height: 38px;
    span {
      float: left;
    }
    span:nth-of-type(1) {
      color: #ff3f3f;
      margin-right: 13px;
    }
    input {
      width: 110px;
      height: 38px;
      border: 1px solid #ccc;
      border-radius: 3px;
    }
    span:nth-of-type(3) {
      cursor: pointer;
      width: 136px;
      height: 38px;
      border: 1px solid #d9d9d9;
      background: #f6f6f6;
      display: inline-block;
      text-align: center;
      line-height: 38px;
      margin: 0 0 0 12px;
    }
    .active {
      cursor: pointer;
      width: 136px;
      height: 38px;
      border: 1px solid #d9d9d9;
      background: #f6f6f6;
      display: inline-block;
      text-align: center;
      line-height: 38px;
      margin: 0 0 0 12px;
    }
    .actives {
      cursor: pointer;
      width: 136px;
      height: 38px;
      border: 1px solid #d9d9d9;
      background: #f6f6f6;
      display: inline-block;
      text-align: center;
      line-height: 38px;
      margin: 0 0 0 12px;
    }
  }
  button {
    cursor: pointer;
    border-radius: 3px;
    width: 110px;
    height: 38px;
    background: #afd129;
    text-align: center;
    line-height: 38px;
    color: #fff;
    font-size: 12px;
    margin: 30px 0 0 296px;
  }
  .samll {
    position: relative;
  }
  .duihao {
    position: absolute;
    top: -13px;
    left: 292px;
  }
  .success {
    font-size: 18px;
    color: #afd129;
    margin: 85px 0 0 375px;
  }
  .bangding {
    font-size: 12px;
    color: #666;
    margin: 19px 0 0 375px;
    span {
      color: #ff0000;
    }
  }
  .goback {
    font-size: 12px;
    color: #ff0000;
    margin: 39px 0 0 375px;
    cursor: pointer;
  }
  .numbers {
    margin: 51px 0 0 194px;
    display: block;
    span {
      color: #ff3f3f;
      margin-right: 13px;
    }
    input {
      width: 280px;
      height: 38px;
      border: 1px solid #ccc;
      border-radius: 3px;
    }
  }
  .dangqian {
    margin: 70px 0 0 152px;
    span {
      color: #438ed1;
    }
  }
  .slet {
    margin: 0 0 26px 148px;
    span {
      color: #ff3f3f;
      margin-right: 13px;
    }
    select {
      padding: 10px;
      width: 120px;
      height: 38px;
      border: 1px solid #ccc;
      font-size: 12px;
      color: #ccc;
      border-radius: 3px;
      outline: none;
    }
  }
  .name {
    margin: 0 0 0 240px;
    span {
      color: #ccc;
    }
  }
  .denglu {
    margin: 24px 0 0 215px;
    span {
      color: #ff3f3f;
      margin-right: 13px;
    }
    input {
      width: 280px;
      height: 38px;
      border: 1px solid #ccc;
      border-radius: 3px;
    }
  }
  .pass {
    margin: 0 0 0 230px;
    span {
      color: #ff3f3f;
      margin-right: 13px;
    }
    input {
      width: 260px;
      height: 38px;
      border: 1px solid #ccc;
      border-radius: 3px;
    }
    span:nth-of-type(2) {
      color: #fa4862;
      margin-left: 13px;
      img {
        margin-right: 11px;
      }
    }
  }
  .newAddress {
    margin-left: 194px !important;
  }
}
</style>