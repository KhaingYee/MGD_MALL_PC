<template>
    <div class="account">
        <!-- <common-header></common-header> -->
        <common-header v-on:childToParent="onChildClick"></common-header>
        <div class="logo">
            <router-link to="/home">
                <!-- <img src="../../assets/img/ban_head.jpg" /> -->
                <img :src="URL + logo_name" />
            </router-link>
            <p>积分结算页</p>
        </div>
        <div class="center">
            <ul class="buzhou">
                <li class="l active">1</li>
                <li class="l active">2</li>
                <li class="l">3</li>
            </ul>
            <ul class="zhushi">
                <li class="l actives">我的购物车</li>
                <li class="l actives">确认订单信息</li>
                <li class="l">成功提交订单</li>
            </ul>
            <div class="dizhi">
                <address-operation @transferAddress="transferAddress"></address-operation>
            </div>
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
                        <ul class>
                            <li>
                                <img class="l shopImg" :src="URL + SettleAccounts.pic_url" alt />
                                <p class="l">{{SettleAccounts.title}}</p>
                                <p class="l" v-if="SettleAccounts.status==0">不可兑换</p>
                                <p class="l" v-else>可以兑换</p>
                                <p class="l">{{SettleAccounts.price_member}}</p>
                                <p class="l">{{SettleAccounts.goods_num}}</p>
                                <p class="l">{{SettleAccounts.youhui}}</p>
                                <p class="r">{{SettleAccounts.integral}}积分</p>
                            </li>
                        </ul>
                    </div>
                </div>
                <div class="jiesuan">
                    <div class="left">
                        <span>留言信息：</span>
                        <textarea v-model="message" class="textarea"></textarea>
                    </div>
                    <div class="right">
                        <p>
                            <span>1</span>件商品，商品总金额：
                            <span>￥{{(SettleAccounts.price_member * SettleAccounts.goods_num).toFixed(2)}}</span>
                        </p>
                        <!-- <p>{{discountData.memberLevel}}:{{Number(discountData.discount)/10}}折优惠</p> -->

                        <div v-if="SettleAccounts.freight_type == 0">
                            <p v-if="deliveryMoney == -1">配送费：免配送费</p>
                            <p v-else-if="deliveryMoney">配送费：{{deliveryMoney|keep2Num}}</p>
                            <p v-else-if="freight == 0">运费：卖家包邮</p>
                            <p v-else>运费：{{freight|keep2Num}}</p>
                        </div>
                        <div v-if="SettleAccounts.freight_type == 1">
                            <p v-if="deliveryMoney == -1">配送费：免配送费</p>
                            <p v-else-if="deliveryMoney">配送费：{{deliveryMoney|keep2Num}}</p>
                            <p v-else-if="freight == 0">运费：卖家包邮</p>
                            <p v-else>运费：{{freight|keep2Num}}</p>
                        </div>
                        <div v-if="SettleAccounts.freight_type == 2">
                            <p>运费：卖家包邮</p>
                        </div>

                    </div>
                </div>
                <div class="pay">
                    <p class="hint">温馨提示：积分兑换需付最低0.01元</p>
                    <p class="yingfu r">
                        应付金额：
                        <span v-if="deliveryMoney == -1">￥{{(SettleAccounts.price_member * SettleAccounts.goods_num).toFixed(2)}}</span>
                        <span v-else-if="deliveryMoney">￥{{((SettleAccounts.price_member * SettleAccounts.goods_num) +deliveryMoney).toFixed(2)}}</span>
                        <span v-else>￥{{((SettleAccounts.price_member * SettleAccounts.goods_num) +freight).toFixed(2)}}</span>
                    </p>
                    <div class="r">
                        <p class="l">
                            寄送至：
                            <span>{{defaultAddress.prov_name}} {{defaultAddress.city_name}} {{defaultAddress.dist_name}} {{defaultAddress.address}}</span>
                        </p>
                        <p class="l">
                            收货人：
                            <span>{{defaultAddress.realname}} {{defaultAddress.mobile}}</span>
                        </p>
                    </div>
                    <br />
                </div>
                <div class="submit">
                    <p class="r" @click="buy">
                        <span>提交订单</span>
                    </p>
                </div>
            </div>
        </div>
        <foot-com></foot-com>
    </div>
</template>

<script>
import QS from "qs";
import img from "@/assets/img/buychahu.jpg";
import selectArea from "../../common/selectionArea.vue";
import { Message } from "element-ui";
import config from "../../httpConfig"
export default {
    data() {
        return {
            data: [],
            logo_name: "",
            SettleAccounts: [],
            defaultAddress: {},
            freight: 0, // 运费
            deliveryMoney:'',
            // discountData:{},
            message: "",
            not_distribution: true,
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
    created(index, selId) {
        // this.SettleAccounts = this.$route.params.setAcc
        this.HTTP(this.$httpConfig.regionLists, {}, "post").then(res => {
            this.data = res.data.data;
        });
        this.getFootData();
        this.getFavIcon(); 
        this.toSettles();
    },
    methods: {
        toSettles() {
            this.HTTP(
                this.$httpConfig.IntegraltoSettleAccounts,
                {
                    id: this.$route.params.id,
                    goods_num: this.$route.params.num
                },
                "post", false)
                .then(res => {
                    this.SettleAccounts=res.data.data;
                    // this.discountData = res.data.data.discount;
                })
                .catch(err => {
                    if (!err.data.status) {
                        this.$alert(err.data.message, "提示", {
                            confirmButtonText: "确定",
                            center: true,
                            lockScroll: false,
                            type: "warning"
                        });
                    }
                });
        },
        getFootData() {
            this.HTTP(this.$httpConfig.aboutEtcetera, {}, "post")
                .then(res => {
                    this.logo_name = res.data.data.logo_name;
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
        buy() {
            if (!this.defaultAddress.id) {
                this.$alert("当前收货人信息不对，请修改后再提交", "提示", {
                    confirmButtonText: "确定",
                    center: true,
                    lockScroll: false,
                    type: "warning"
                });
                return false;
            }
            if (!this.not_distribution) {
                this.$alert("当前地址部分卖家暂无配送", "提示", {
                    confirmButtonText: "确定",
                    center: true,
                    lockScroll: false,
                    type: "warning"
                });
                return false;
            }
            if (this.SettleAccounts.status == 0) {
                this.message("该商品不可兑换", {
                    type: "warninng"
                });
                return;
            }
            this.HTTP(
                this.$httpConfig.integralOrder,
                {
                    remarks: this.message,
                    address_id: this.defaultAddress.id
                },
                "post"
            ).then(res => {
                if (res.data.status == 1) {
                    if(this.deliveryMoney == -1){
                    var total = (this.SettleAccounts.price_member * this.SettleAccounts.goods_num).toFixed(2);
                    }
                    else if(this.deliveryMoney){
                    var total = ((this.SettleAccounts.price_member * this.SettleAccounts.goods_num)+this.deliveryMoney).toFixed(2);	
                    }
                    else if(this.freight){
                    var total = ((this.SettleAccounts.price_member * this.SettleAccounts.goods_num)+this.freight).toFixed(2);
                    }
                    else {
                        var total = res.data.data.money
                    }
                    this.$router.push({
                        path: "/integralPay",
                        query: {
                            total_price: total,
                            intergral: res.data.data.integral
                        }
                    });
                }
            })
            .catch(err => {
                console.log(err);
            });
        },
        transferAddress(address) {
            this.defaultAddress = address;
            this.getFreight(this.defaultAddress);
            this.getDistributionFee();
        },
        getDistributionFee() { //获取配送端
            this.HTTP(this.$httpConfig.deliveryMoney, {
            store_id: this.SettleAccounts.store_id,
            address_id: this.defaultAddress.id,
            goods_id: this.$route.params.id
            }, 'post').then((res) => {
                this.deliveryMoney = parseFloat(res.data.data.money);
            })
        },
        getFreight(data) {
            this.HTTP(
                this.$httpConfig.getFreight,
                {
                    prov_id: ~~data.prov,
                    city_id: ~~data.city,
                    dist_id: ~~data.dist
                },
                "post",
                false
            )
                .then(res => {
                    this.not_distribution = true;
                    this.freight = parseFloat(res.data.data);
                })
                .catch(res => {
                    this.not_distribution = false;
                    this.$alert("当前地址部分卖家暂无配送", "提示", {
                        confirmButtonText: "确定",
                        center: true,
                        lockScroll: false,
                        type: "warning"
                    });
                });
        },
    }
};
</script>

<style lang="less" scoped>
.l {
    float: left;
}

// .jiesuan {
// 	display: flex;
// 	flex-direction: row;
// }
// .left, .right {
// 	display: flex;
// 	flex-direction: column;
// }
.textarea {
    resize: none;
    display: inline-block;
    margin-bottom: 20px;
}

.shopImg {
    width: 85px;
    height: 65px;
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
    }
    p {
        font-size: 24px;
        float: left;
        margin: 45px 0 0 29px;
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
            margin-bottom: 10px;
            cursor: pointer;
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
                height: 34px;
                margin: 16px 0 10px;
            }
            p:nth-of-type(3) {
                color: #d02629;
            }
        }
        .hover {
            border-color: #d02629 !important;
            background: url(../../assets/img/right.jpg) no-repeat right 0 bottom
                0;
        }
        li:nth-child(4n) {
            margin-right: 0;
        }
    }
    .guanli {
        overflow: hidden;
        margin: 15px 0 12px 0;
        span {
            width: 84px;
            height: 25px;
            text-align: center;
            line-height: 25px;
            float: left;
            font-size: 12px;
            cursor: pointer;
        }
        span:nth-of-type(1) {
            background: #d02629;
            color: #fff;
            margin-right: 18px;
        }
        span:nth-of-type(2) {
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
        }
        ul {
            margin: 30px 0 57px 0;
            li {
                height: 80px;
                background: #fcfcfc;
                margin-bottom: 1px;
                img {
                    margin: 16px 9px 0 13px;
                }
                p {
                    font-size: 12px;
                    color: #666;
                    margin-top: 17px;
                }
                p:nth-of-type(1) {
                    width: 192px;
                    margin-right: 93px;
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
            .heji {
                height: 46px;
                line-height: 46px;
                text-align: right;
                font-size: 12px;
                color: #616161;
                span {
                    font-size: 13px;
                    color: #df7344;
                    margin: 0 54px 0 54px;
                }
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
    background: #eaeaea;
    text-align: right;
    margin-top: 70px;
    padding-bottom: 30px;
    height: 130px;
    .hint {
        margin-top: 10px;
        float: right;
        margin-right: 25px;
    }
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
    margin-top: 45px;
    margin-bottom: 136px;
    color: white;
    p:nth-of-type(1) {
        width: 182px;
        height: 38px;
        background: #ff5100;
        text-align: center;
        line-height: 38px;
        cursor: pointer;
        a {
            color: #fff;
        }
    }
    p:nth-of-type(2) {
        font-size: 12px;
        color: #3969cf;
        margin: 15px 38px 0 0;
        cursor: pointer;
    }
}

.contents {
    margin: 20px 0 143px 0;
    div {
        text-align: left;
        font-size: 12px;
        overflow: hidden;
        span {
            color: #ff2626;
        }
    }
    div:nth-of-type(1) {
        p {
            font-size: 12px;
        }
        select {
            width: 90px;
            height: 24px;
        }
        p:nth-of-type(2) {
            width: 363px;
            height: 24px;
            margin-left: 8px;
            .behind {
                width: 355px;
                height: 24px;
                outline: none;
                margin-left: 10px;
                color: #333;
                border-color: #ccc;
            }
        }
    }
    div:nth-of-type(2) {
        margin-top: 15px;
        p {
            font-size: 12px;
        }
        textarea {
            width: 463px;
            height: 85px;
            padding: 10px 0 0 18px;
            border-color: #ccc;
        }
    }
    div:nth-of-type(3) {
        padding-left: 5px;
        margin-top: 15px;
        input {
            width: 281px;
            height: 24px;
            border: 1px solid #ccc;
        }
    }
    div:nth-of-type(4) {
        margin-left: -11px;
        margin-top: 15px;
        input {
            width: 281px;
            height: 24px;
            border: 1px solid #ccc;
        }
    }
    div:nth-of-type(5) {
        margin-top: 15px;
        input {
            width: 281px;
            height: 24px;
            border: 1px solid #ccc;
        }
    }
    div:nth-of-type(6) {
        padding-left: 5px;
        margin-top: 15px;
        input {
            width: 281px;
            height: 24px;
            border: 1px solid #ccc;
        }
    }
    .moren {
        margin-top: 21px;
        font-size: 12px;
        margin-left: 64px;
        input {
            margin: 2px 8px 0 0;
            float: left;
        }
    }
    .baocun {
        cursor: pointer;
        font-size: 12px;
        margin-left: 64px;
        height: 32px;
        width: 72px;
        border-radius: 3px;
        background: #ff6000;
        text-align: center;
        line-height: 32px;
        color: #fff;
        margin-top: 15px;
    }
}
</style>