<template>
    <div id="newPassword">
        <div id="topLogo">
            <div class="center">
                <ul>
                    <li class="l logo">
                        <img :src="URL + logoPhoto" />
                    </li>
                    <li class="l category">
                        <img src="../../assets/img/find_text.png" />
                    </li>
                </ul>
            </div>
        </div>
        <div id="codeNmae">
            <div class="center">
                <div class="numberOne">
                    <p>
                        <img class="sink" src="../../assets/img/buzhou2.png" />
                    </p>
                    <div class="part">
                        <!-- <p class="bottomOne l">账户名</p> -->
                        <p class="bottomTwo l">用户身份</p>
                        <p class="bottomTwo l">设置新密码</p>
                        <p class="bottomTwo l">完成</p>
                    </div>
                    <div class="nameCode">
                        <p class="l accept">
                            <i>*</i>
                            新密码：<input type="password" v-model="password" />
                        </p>
                        <p class="l accepts">
                            <i><img src="../../assets/img/warn.png"/></i>
                            <span class="changeColor">请设置密码</span>
                        </p>
                    </div>
                    <span class="button next" @click="to">下一步</span>
                </div>
            </div>
        </div>
        <com-foot></com-foot>
    </div>
</template>

<script>
import { Message } from "element-ui";
import ComFoot from "@/common/footerDetail.vue";

export default {
    components: {
        ComFoot
    },
    data() {
        return {
            password: "",
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
      this.getFootData();
      this.getFavIcon();
      console.log(this.$route.params.tel); 
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
        to() {
            if (this.$store.state.loginMethod == 0) {
                var data = {
                    mobile: this.$route.params.tel,
                    verify: this.$route.params.verify,
                    password: this.password,
                    re_password: this.password
                }
            }
            if (this.$store.state.loginMethod == 1) {
                var data = {
                    email: this.$route.params.email,
                    verify: this.$route.params.verify,
                    password: this.password,
                    re_password: this.password
                }
            }
            this.HTTP(
                this.$httpConfig.backPwd,
                    data,
                "post"
            ).then(res => {
                if (res.data.status === 1 && res.data.data) {
                    this.$router.push("findFour");
                    // Message({
                    // 	message: '',
                    // 	type: 'info',
                    // 	duration: 1000
                    // })
                }
            });
        }
    }
};
</script>

<style lang="less" scoped>
.center {
    width: 1000px;
    height: 100%;
    margin: 0 auto;
}

.l {
    float: left;
}

.r {
    float: right;
}
#topLogo {
    width: 100%;
    height: 100px;
    .center {
        height: 100px;
        ul {
            width: 100%;
            height: 100px;
            .logo {
                width: 184px;
                height: 53px;
                margin-top: 21px;
                border-right: 1px solid #ebebeb;
                img{
                    width: 184px;
                    height: 53px;
                }
            }
            .category {
                margin-top: 35px;
                margin-left: 15px;
            }
        }
    }
}
#codeNmae {
    width: 100%;
    height: 447px;
    .center {
        height: 447px;
        .numberOne {
            width: 100%;
            height: 447px;
            border: 1px solid #dddddd;
            .sink {
                margin-top: 74px;
                margin-left: 150px;
            }
            .part {
                width: 100%;
                height: 24px;
                margin-left: 151px;
                margin-top: 10px;
                .bottomOne {
                    width: 178px;
                    height: 24px;
                    line-height: 24px;
                    text-align: center;
                    margin-left: 150px;
                    color: #afd129;
                    font-size: 12px;
                }
                .bottomTwo {
                    width: 240px;
                    height: 24px;
                    line-height: 24px;
                    text-align: center;
                    color: #d0d0d0;
                    font-size: 12px;
                }
            }
            .nameCode {
                width: 714px;
                height: 38px;
                margin-left: 290px;
                margin-top: 70px;

                .accept {
                    width: 340px;
                    height: 38px;
                    i {
                        color: red;
                        margin-right: 5px;
                    }
                    input {
                        width: 260px;
                        height: 38px;
                        text-indent: 0.5em;
                        border: 1px solid #cccccc;
                    }
                }
                .accepts {
                    width: 140px;
                    height: 38px;
                    line-height: 38px;
                    .abtain {
                        display: inline-block;
                        width: 136px;
                        height: 38px;
                        border: 1px solid #d9d9d9;
                        text-align: center;
                        background: #f6f6f6;
                        cursor: default;
                        margin-right: 15px;
                    }
                    .changeColor {
                        color: red;
                    }
                }
            }
            .next {
                width: 100px;
                height: 38px;
                background: #afd129;
                border: 0;
                color: #ffffff;
                font-size: 12px;
                margin-left: 360px;
                margin-top: 20px;
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
            margin-left: 369px;
            li {
                width: 60px;
                height: 12px;
                border-right: 1px solid #8b8b8b;
                font-size: 12px;
                text-align: center;
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
