<template>
    <div id="people">
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
                        <img class="sink" src="../../assets/img/buzhou1.png" />
                    </p>
                    <div class="part">
                        <p class="bottomTwo l">用户身份</p>
                        <p class="bottomTwo l">设置新密码</p>
                        <p class="bottomTwo l">完成</p>
                    </div>
                    <div class="nameCode">
                        <!-- <p class="checkIphone">
							<span class="deep">验证方式：</span>
							<span class="deeply">已验证手机</span>
						</p> -->
                        <p class="checkIphone" v-if="$store.state.loginMethod == 0">
                            <span class="deep">手机：</span>
                            <span class="deeply">
                                <input
                                    type="text"
                                    name=""
                                    v-model="mobile"
                                    id=""
                                    placeholder="请输入手机号"
                                />
                            </span>
                        </p>
                        <p class="checkIphone" v-if="$store.state.loginMethod == 1">
                            <span class="deep">邮箱：</span>
                            <span class="deeply">
                                <input
                                    type="text"
                                    name=""
                                    v-model="mailAcc"
                                    id=""
                                    placeholder="请输入常用邮箱"
                                />
                            </span>
                        </p>
                        <p class="l accept" v-if="$store.state.loginMethod == 0">
                            <i>*</i>手机验证码：<input
                                type="text"
                                v-model="verify"
                            />
                        </p>
                        <p class="l accept" v-if="$store.state.loginMethod == 1">
                            <i>*</i>邮箱验证码：<input
                                type="text"
                                v-model="verify"
                            />
                        </p>
                        <p class="l accepts">
                            <span
                                class="abtain"
                                :class="{ active: isactive }"
                                @click="abtain"
                                >{{ btnText }}</span
                            >
                            <i><img src="../../assets/img/warn.png"/></i>
                            <span v-if="$store.state.loginMethod == 0" class="changeColor">手机验证码不能为空</span>
                            <span v-if="$store.state.loginMethod == 1" class="changeColor">邮箱验证码不能为空</span>
                        </p>
                    </div>
                    <span class="button next" @click="to">下一步</span>
                    <!-- <router-link :to="{name: 'findThree', params: {tel: this.mobile, verify: this.verify}}" class="button next"  @click="to">下一步</router-link> -->
                </div>
            </div>
        </div>
        <com-foot @handleItemInfo="handleItemInfo"></com-foot>
    </div>
</template>

<script>
import Qs from "qs";
import { Message } from "element-ui";
import ComFoot from "@/common/footerDetail.vue";
export default {
    components: {
        ComFoot
    },
    data() {
        return {
            name: "",
            isactive: "",
            btnText: "获取验证码",
            mobile: "",
            mailAcc: "",
            verify: "",
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
        // let title = "找回密码2" + this.$constant.webComContent;
        // this.showScroll.scrollTitle(title);
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
           handleItemInfo(data) {
            // let title =
            //     "找回密码2" +
            //     "-" +
            //     data.intnet_title +
            //     "-" +
            //     data.init_key_word +
            //     " ";
               let title = sessionStorage.getItem('titleKey') + '-' +sessionStorage.getItem('updateDescription');
            this.showScroll.scrollTitle(title);

        },
        getFootData() {
            this.HTTP(this.$httpConfig.aboutEtcetera, {}, "post")
                .then(res => {
                    sessionStorage.setItem(
                        "titleKey",
                        res.data.data.intnet_title
                    );
                    sessionStorage.setItem("updateDescription", res.data.data.intnet_description);
                    sessionStorage.setItem("contentKey", res.data.data.init_key_word);
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
                if (this.mobile === "") {
                    Message({
                        message: "手机号不能为空",
                        type: "info",
                        duration: 1000
                    });
                    return;
                } else if (!/^1[345789]\d{9}$/.test(this.mobile)) {
                    Message({
                        message: "请输入正确的手机号",
                        type: "info",
                        duration: 1000
                    });
                    return;
                }
                if (this.verify === "") {
                    Message({
                        message: "验证码不能为空",
                        type: "info",
                        duration: 1000
                    });
                    return;
                }
                this.$router.push({
                    name: "findThree",
                    params: {
                        tel: this.mobile,
                        verify: this.verify
                    }
                });
            }

            if (this.$store.state.loginMethod == 1) {
                if (this.mailAcc === "") {
                    Message({
                        message: "邮箱不能为空",
                        type: "info",
                        duration: 1000
                    });
                    return;
                } else if (
                    !/^(([^<>()[\]\\.,;:\s@"]+(\.[^<>()[\]\\.,;:\s@"]+)*)|(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/.test(
                        this.mailAcc
                    )
                ) {
                    Message({
                        message: "请输入正确的手邮箱",
                        type: "info",
                        duration: 1000
                    });
                    return;
                }
                if (this.verify === "") {
                    Message({
                        message: "验证码不能为空",
                        type: "info",
                        duration: 1000
                    });
                    return;
                }
                this.$router.push({
                    name: "findThree",
                    params: {
                        email: this.mailAcc,
                        verify: this.verify
                    }
                });
            }
        },
        abtain() {
            if (this.$store.state.loginMethod == 0) {
                if (this.mobile === "") {
                    Message({
                        message: "手机号不能为空",
                        type: "info",
                        duration: 1000
                    });
                    return;
                } else if (!/^1[345789]\d{9}$/.test(this.mobile)) {
                    Message({
                        message: "请输入正确的手机号",
                        type: "info",
                        duration: 1000
                    });
                    return;
                }
                // var N = 60,
                //     _this = this,
                //     clear = null;
                // _this.isActive = true;
                // _this.btnText = "请" + N + "秒后重试";
                // _this.isActive = true;
                // clear = setInterval(function() {
                //     _this.btnText = "请" + N-- + "秒后重试";
                //     if (N < 0) {
                //         clearInterval(clear);
                //         _this.btnText = "获取验证码";
                //         _this.isActive = false;
                //     }
                // }, 1000);
                this.HTTP(
                    this.$httpConfig.smsLogin,
                    {
                        mobile: this.mobile
                    },
                    "post"
                ).then(res => {
                    if (res.data.status == 1) {
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
                        this.token = res.data.data.token;
                    } else {
                        clearInterval(clear);
                        _this.btnText = "再次获取验证码";
                        _this.isActive = false;
                    }
                });
            }

            if (this.$store.state.loginMethod == 1) {
                if (this.mailAcc === "") {
                    Message({
                        message: "邮箱不能为空",
                        type: "info",
                        duration: 1000
                    });
                    return;
                } else if (
                    !/^(([^<>()[\]\\.,;:\s@"]+(\.[^<>()[\]\\.,;:\s@"]+)*)|(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/.test(
                        this.mailAcc
                    )
                ) {
                    Message({
                        message: "请输入正确的手邮箱",
                        type: "info",
                        duration: 1000
                    });
                    return;
                }
                this.HTTP(
                    this.$httpConfig.getSendMailboxByPassword,
                    {
                        email: this.mailAcc
                    },
                    "post"
                ).then(res => {
                    if (res.data.status == 1) {
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
                        this.token = res.data.data.token;
                    } else {
                        clearInterval(clear);
                        _this.btnText = "再次获取验证码";
                        _this.isActive = false;
                    }
                });
            }
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

.active {
    width: 80px;
    height: 24px;
    line-height: 24px;
    border-radius: 10px;
    border: 1px solid #c4c4c4;
    font-size: 12px;
    color: #a9a9a9;
    text-align: center;
    margin-top: 6px;
    position: absolute;
    top: 26px;
    left: 220px;
    cursor: pointer;
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
                height: 142px;
                margin-left: 290px;
                margin-top: 30px;
                .checkIphone {
                    width: 100%;
                    height: 56px;
                    line-height: 56px;
                    .deep {
                        color: #666666;
                        font-size: 14px;
                    }
                    .deeply {
                        color: #999999;
                        font-size: 14px;
                        input[type="text"] {
                            width: 200px;
                            height: 30px;
                            text-indent: 0.5em;
                            border: 1px solid #cccccc;
                        }
                    }
                }
                .accept {
                    width: 240px;
                    height: 38px;
                    i {
                        color: red;
                        margin-right: 3px;
                    }
                    input {
                        width: 110px;
                        height: 38px;
                        text-indent: 0.5em;
                        border: 1px solid #cccccc;
                    }
                }
                .accepts {
                    width: 340px;
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
                        cursor: pointer;
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
                margin-left: 300px;
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
