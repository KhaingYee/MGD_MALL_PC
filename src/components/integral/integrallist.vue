<template>
    <div class="list">
        <common-header v-on:childToParent="onChildClick"></common-header>
        <head-com :navid = 58></head-com>
        <div class="center">
            <div class="header">
                <img class="l logo" src="../../assets/img/fangzi.jpg" />
                <ul class="l">
                    <li class="l">
                        <router-link to="home">首页</router-link>
                    </li>
                    <li class="l">></li>
                    <li class="l">
                        <router-link to="integral">积分商城</router-link>
                    </li>
                    <li class="l">></li>
                    <li class="l">
                        <router-link to>所有分类</router-link>
                    </li>
                </ul>
            </div>
            <div class="fenlei">
                <p>积分分类</p>
                <div class="shangpin both">
                    <ul id="zli" ref="brandHeight">
                        <li class="l">商品分类：</li>
                        <li
                            class="l"
                            v-for="(shangpins,index) in TopClassData"
                            :key="index"
                            :style="select.one === index ? 'color: red;' : ''"
                            @click="selectTopClass(shangpins.id, index)"
                        >{{shangpins.class_name}}</li>
                    </ul>
                    <i @click="down" v-show="find" class="el-icon-arrow-down"></i>
                    <i @click="down" v-show="lose" class="el-icon-arrow-up"></i>
                    <div class="duo" @click="opens(1)" v-if="show" :class="{'duo':onoff}">+多选</div>
                </div>
                <!-- <div class="both">
                    <ul ref="priceHeight">
                        <li class="l">品 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 牌：</li>
                        <li
                            class="l"
                            v-for="(pinpais,index) in BrandList"
                            :key="index"
                            :style="select.two === index ? 'color: red;' : ''"
                            @click="selectBrand(pinpais.id, index)"
                        >{{pinpais.brand_name}}</li>
                    </ul>
                    <i @click="up" v-show="block" class="el-icon-arrow-down"></i>
                    <i @click="up" v-show="hidden" class="el-icon-arrow-up"></i>
                    <div class="duo" @click="opens(2)" v-if="show1" :class="{'duo':onoff1}">+多选</div>
                </div> -->
                <!-- <div v-if="!$route.query.integral" class="both jifen"> -->
                    <div class="both jifen">
                    <ul>
                        <li class="l">积分范围：</li>
                        <li
                            v-for="(item, index) in limit"
                            :key="index"
                            @click="selectIntegral(item, index)"
                            :style="select.three === index ? 'color: red;' : ''"
                        >{{item.inte.replace(',', '-')}}</li>
                    </ul>
                </div>
            </div>
            <div class="thead">
                <ul class="l">
                    <li class="l">排序方式：</li>
                    <li class="l" @click="change(0)" :class="{active:isactive==0}">默认</li>
                    <li class="l" @click="change(1)" :class="{actives:isactive==1}">积分值</li>
                    <li class="l" @click="change(2)" :class="{actives:isactive==2}">上架时间</li>
                </ul>
                <div class="r">
                    <p class="l">
                        <input type="checkbox" v-model="select.queried" @click="checkbox" />只查看我能兑换
                    </p>
                    <p class="r">
                        <span @click="hit(0)" :class="{bg:isbg==0}">上一页</span>
                        <span @click="hit(1)" :class="{bg:isbg==1}">下一页</span>
                    </p>
                </div>
            </div>
            <div class="base">
                <ul>
                    <li
                        class="l"
                        v-for="(lis, index) in integralGoodsList.slice((currentPage-1)*pagesize,currentPage*pagesize)"
                        :key="index"
                    >
                        <img
                            @click="$router.push({name:'IntegralInside',query: {goods_id: lis.goods_id,id:lis.id,integral:lis.integral}})"
                            :src="URL+lis.pic_url"
                        />
                        <p
                            @click="$router.push({name:'IntegralInside',query: {goods_id: lis.goods_id,id:lis.id,integral:lis.integral}})"
                            class="jieshao"
                        >{{lis.title}}</p>
                        <div class="bottom">
                            <p class="l">
                                价格
                                <!-- <span>￥{{lis.price_member}}</span> -->
                                <span>￥{{lis.money}}</span>
                            </p>
                            <p class="r">
                                <span
                                    @click="$router.push({name: 'Settlement', params: {id: lis.id,num: 1}})"
                                    style="color: white;"
                                >立即兑换</span>
                            </p>
                            <p class="l">积分兑换：{{lis.integral}}积分</p>
                        </div>
                        <p @click="goToStore(lis.store_id)" class="shop-show" v-if="lis.shop_name">
                        <span class="show-shopname">{{lis.shop_name}}</span>
                        <img src="../../assets/img/vioback.png" class="shop-btn" v-if="lis.shop_name">
                        </p>
                        <el-dropdown v-if="lis.store_grade">
                            <span class="first-name">{{lis.store_grade}}</span>
                            <el-dropdown-menu slot="dropdown" v-if="lis.classification">
                                <el-dropdown-item>{{lis.classification}}</el-dropdown-item>
                            </el-dropdown-menu>
                        </el-dropdown>
                    </li>
                </ul>
            </div>
            <div class="fenye">
                <!-- <el-pagination
                    @size-change="handleSizeChange"
                    @current-change="handleCurrentChange"
                    :current-page="currentPage"
                    :page-sizes="[30, 60, 90, 120]"
                    layout="total, sizes, prev, pager, next, jumper"
                    :total="integralGoodsList.length"
                ></el-pagination> -->

                <el-pagination
                    @size-change="handleSizeChange"
                    @current-change="handleCurrentChange"
                    :current-page.sync="currentPage"
                    :page-size="count"
                    layout="prev, pager, next, jumper"
                    :total="totalPages"
                ></el-pagination>
                <!-- <div class="fenyes">
                    <span class="l">上一页</span>
                    <ul class="l">
                        <li class="l" v-for="(yeshus, index) in yeshu" :key="index">
                            <a href="/">{{yeshus}}</a>
                        </li>
                    </ul>
                    <span class="l">下一页></span>
                    <div class="l gong">共{{totalPages}}页</div>
                    <div class="l go">
                        去第
                        <input type="text" /> 页
                    </div>
                </div>-->
            </div>
        </div>
        <foot-com></foot-com>
    </div>
</template>

<script>
import imgs from "@/assets/img/hu.jpg";
import img from "@/assets/img/xiafashui.png";
import guo from "@/assets/img/dianguo.png";
export default {
    data() {
        return {
            computedResult: [],
            shangpin: [],
            pinpai: [],
            onoff: false,
            onoff1: false,
            show: false,
            show1: false,
            lose: false,
            find: true,
            off: false,
            block: true,
            hidden: false,
            isbg: "",
            isactive: "",
            liarr: [],
            li: [],
            yeshu: [],
            pagesize: 30,
            TopClassData: [],
            // BrandList: [],
            integralGoodsList: "",
            count: 0,
            totalPages: 0,
            currentPage: 1,
            limit: [
                {id: 1, inte: "0,500" },
                {id: 2, inte: "500,1000" },
                {id: 2, inte: "1000,2000" },
                {id: 3, inte: "2000,5000" },
                {id: 4, inte: "5000,10000" }
                // "0,500"
                // "500,1000",
                // "1000,2000",
                // "2000-5000",
                // "5000-10000"
            ],
            integralId: "",
            integralLimit: "",
            select: {
                one: undefined,
                two: undefined,
                three: undefined,
                queried: false
            },
            temporary: [], //临时保存商品分类值
            temporary1: [],
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
        //获取商品一级分类
        this.getTopClass();
        //获取商品品牌
        // this.getBrandList();
        // 获取所有积分商品列表
        this.getIntegralGoodsList();
        this.Sort([1, 2, 6, 123, 4, 6, 8, 4, 7, 3, 8, 8, 12, 34, 15, 63]);

        this.show = this.$refs.brandHeight.clientHeight > 55 ? true : false;
        // this.show1 = this.$refs.priceHeight.clientHeight > 55 ? true : false;
        var self = this
            window.setTimeout(function () {
                self.headParams.title = sessionStorage.titleKey
                self.headParams.description = sessionStorage.updateDescription
                self.headParams.keywords = sessionStorage.contentKey
                self.headParams.link = sessionStorage.pcfavicon
                self.$emit('updateHead')
        }, 3000)
    },
    created() {
        this.getFootData();
        this.getFavIcon(); 
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
        goToStore(id) {
            sessionStorage.setItem('store_id',id);
            window.open(window.location.origin + '/storehome?id=' + id)
        },
        onChildClick (value) {
            this.fromChild = value
            if(this.fromChild == 'false') {
                location.reload();
            }
        },
        handleSizeChange: function(size) {
            this.pagesize = size;
        },
        handleCurrentChange: function(currentPage) {
            this.currentPage = currentPage;
        },
        timestampToTime(cjsj) {
            var date = new Date(cjsj); //时间戳为10位需*1000，时间戳为13位的话不需乘1000
            var Y = date.getFullYear() + "-";
            var M =
                (date.getMonth() + 1 < 10
                    ? "0" + (date.getMonth() + 1)
                    : date.getMonth() + 1) + "-";
            var D = date.getDate() + " ";
            var h = date.getHours() + ":";
            var m = date.getMinutes() + ":";
            var s = date.getSeconds();
            return Y + M + D + h + m + s;
        },
        change(index) {
            this.isactive = index;
            this.liarr = this.li[index];
            if (index == 2) {
                this.integralGoodsList.sort((a, b) => {
                    return a.create_time - b.create_time;
                });
            }
            if (index === 1) {
                this.integralGoodsList.sort((a, b) => {
                    return b.integral - a.integral;
                });
            }
        },
        //多选隐藏
        opens(s) {
            if (s == 1) {
                this.onoff = !this.onoff;
            } else {
                this.onoff1 = !this.onoff1;
            }
        },
        Sort(a, b) {
            return a - b;
        },
        // 查看我能兑换
        checkbox() {
            this.select.queried = !this.select.queried;
            this.HTTP(
                this.$httpConfig.getIntegralGoodsList,
                {
                    queried: this.select.queried
                },
                "post"
            ).then(res => {
                let data = res.data.data;
                this.integralGoodsList = data.data;
            });
        },
        // 商品分类筛选
        selectTopClass(id, index) {
            this.select.one = index;
            this.HTTP(this.$httpConfig.getIntegralGoodsList, {
                id: id
            }, "post").then(
                res => {
                    let data = res.data.data;
                    this.integralGoodsList = data.data;
                }
            );
        },
        // 品牌筛选
        selectBrand(id, index) {
            this.select.two = index;
            this.HTTP(
                this.$httpConfig.getIntegralGoodsList,
                {
                    brand_id: id
                },
                "post"
            ).then(res => {
                let data = res.data.data;
                this.integralGoodsList = data.data;
            });
        },
        // 范围筛选
        selectIntegral(item, index) {
            this.select.three = index;
            this.HTTP(
                this.$httpConfig.getIntegralGoodsList,
                {
                    id: item.id,
                    integral: item.inte
                },
                "post"
            ).then(res => {
                let data = res.data.data;
                this.integralGoodsList = data.data;
            });
        },
        //获取所有积分商品列表
        getIntegralGoodsList() {
            // if(this.$route.query.integral) {
            //     var integralPoint = this.$route.query.integral;
            //     if(integralPoint<=500) {
            //         this.integralId = 1;
            //         this.integralLimit = 0 + "," + integralPoint;
            //     }
            //     if(1000<=integralPoint>=500) {
            //         this.integralId = 2;
            //         this.integralLimit = 500 + "," + integralPoint;
            //     }
            //     if(2000<=integralPoint>=1000) {
            //         this.integralId = 3;
            //         this.integralLimit = 1000 + "," + integralPoint;
            //     }
            //     if(5000<=integralPoint>=2000) {
            //         this.integralId = 4;
            //         this.integralLimit = 2000 + "," + integralPoint;
            //     }
            //     if(10000<=integralPoint>=5000) {
            //         this.integralId = 5;
            //         this.integralLimit = 5000 + "," + integralPoint;
            //     }
            //     this.HTTP(this.$httpConfig.getIntegralGoodsList, 
            //     {
            //         id: this.integralId,
            //         integral: this.integralLimit
            //     },
            //     "post").then(
            //         res => {
            //             let data = res.data.data;
            //             this.integralGoodsList = data.data;
            //             this.count = data.per_page;
            //             this.totalPages = data.total;
            //         }
            //     );
            // } else {
                this.HTTP(this.$httpConfig.getIntegralGoodsList, {}, "post").then(
                    res => {
                        let data = res.data.data;
                        this.integralGoodsList = data.data;
                        this.count = data.per_page;
                        this.totalPages = data.total;
                    }
                );
            // }
        },
        down() {
            this.onoff = !this.onoff;
            this.lose = !this.lose;
            this.find = !this.find;
            // 判断商品分类右侧箭头的状态来获取当前显示的值
            if (this.find == true && this.lose == false) {
                this.TopClassData = this.temporary.slice(0, 6);
            } else {
                this.TopClassData = this.temporary;
            }
        },
        // up() {
        //     this.off = !this.off;
        //     this.block = !this.block;
        //     this.hidden = !this.hidden;
        //     if (this.block == true && this.hidden == false) {
        //         //判断多选之前显示八个，之后显示全部
        //         this.BrandList = this.temporary1.slice(0, 8);
        //     } else {
        //         this.BrandList = this.temporary1;
        //     }
        // },
        hit(index) {
            this.isbg = index;
        },

        //获取商品一级分类
        getTopClass() {
            this.HTTP(this.$httpConfig.getTopClass, {}, "post").then(res => {
                var list = res.data.data;
                this.temporary = list; //把结果临时保存进临时数组，用于判断
                this.TopClassData = this.temporary.slice(0, 6);
            });
        },
        //获取商品品牌
        // getBrandList() {
        //     this.HTTP(this.$httpConfig.getBrandList, {}, "post").then(res => {
        //         var list = res.data.data;
        //         this.temporary1 = list;
        //         this.BrandList = this.temporary1.slice(0, 8);
        //     });
        // }
    }
};
</script>
<style lang="less" scoped>
 .el-dropdown{
 width: 230px !important; 
}
.el-dropdown-menu {
    width: 230px !important;
    position: absolute !important;
    margin: 5px 0px 0 -70px !important;
    background-color: #FFF;
    border: 1px solid #de2d35;
    border-radius: 4px;
    -webkit-box-shadow: 0 2px 12px 0 rgba(0,0,0,.1);
    box-shadow: 0 2px 12px 0 rgba(0,0,0,.1);
}
.el-dropdown-menu__item {
  line-height: 6px !important;
  font-size: 12px !important;
  color:rgb(101, 101, 101) !important;
}
  .el-dropdown-menu__item:focus, .el-dropdown-menu__item:not(.is-disabled):hover{
    color:rgb(101, 101, 101) !important;
    background-color: #ffffff !important;
  }

.el-popper .popper__arrow {
  border-width: 6px;
  filter: drop-shadow(0 2px 12px rgba(0,0,0,.03));   
}

.el-popper[x-placement^=bottom] .popper__arrow {
  border-bottom-color: red !important;
}
.el-popper .popper__arrow {
  border-width: 6px;
  filter: drop-shadow(0 2px 12px rgba(0,0,0,.03));
  
}
.el-popper .popper__arrow, .el-popper .popper__arrow:after {
  position: absolute;
  display: block;
  width: 0;
  height: 0;
  border-color: transparent;
  border-style: solid;  
} 
</style>
<style lang="less" scoped>
#zli {
    display: flex;
    flex-wrap: wrap;
    overflow: hidden;
}
.l {
    float: left;
}

.r {
    float: right;
}
ul {
    display: flex;
    flex-wrap: wrap;
}
.center {
    width: 1200px;
    margin: 0 auto;
    height: 100%;
}

.header {
    height: 37px;
    line-height: 37px;
    .logo {
        padding: 13px 8px 0 0;
    }
    li {
        font-size: 12px;
        a {
            color: #a1a1a1;
        }
    }
    li:last-child {
        a {
            color: #555;
        }
    }
}

.fenlei {
    overflow: hidden;
    border: 1px solid #e5e5e5;
    margin-top: 2px;
    p {
        height: 36px;
        background: #f4f4f4;
        color: #333;
        font-size: 13px;
        line-height: 36px;
        padding-left: 13px;
    }
    .both {
        width: 1156px;
        margin-left: 25px;
        border-bottom: 1px dashed #f2f2f2;
        overflow: hidden;
        position: relative;

        li {
            height: 40px;
            line-height: 40px;
            margin-right: 53px;
            display: flex;
            cursor: pointer;
            &:hover {
                color: red;
            }
        }
        li:first-child {
            margin-right: 22px;
            color: #999;
        }
        .buxian {
            color: #cfa940;
        }
        i {
            position: absolute;
            top: 12px;
            right: 7px;
            cursor: pointer;
        }
        .duo {
            width: 45px;
            height: 22px;
            background: #f4f4f4;
            position: absolute;
            top: 8px;
            right: 30px;
            text-align: center;
            line-height: 22px;
            font-size: 12px;
            color: #666;
        }
    }
    .shangpin {
        width: 1150px;
        margin-top: 6px;
    }
    .jifen {
        margin-bottom: 10px;
        border-bottom: 0;
    }
}

.thead {
    height: 41px;
    background: #f9f9f9;
    margin: 12px 0 16px;
    border: 1px solid #e5e5e5;
    border-bottom: 0;
    li {
        line-height: 40px;
        text-align: center;
        border-right: 1px solid #e5e5e5;
        border-bottom: 1px solid #e5e5e5;
        font-size: 12px;
        cursor: pointer;
    }
    li:nth-of-type(1) {
        width: 73px;
    }
    li:nth-of-type(2) {
        width: 49px;
    }
    li:nth-of-type(3) {
        width: 80px;
        background: url(../../assets/img/xiajiantou.png) no-repeat top 17px
            right 10px;
    }
    li:nth-of-type(4) {
        width: 99px;
        background: url(../../assets/img/xiajiantou.png) no-repeat top 17px
            right 10px;
    }
    .active {
        border-bottom: 0;
        background: #fff;
        // color: #cfa942;
        color: #de2d35;
    }
    .actives {
        // color: #cfa942;
        color: #de2d35;
        border-bottom: 0;
        // background: #fff url(../../assets/img/xiajiantou01.png) no-repeat top
        background: #fff url(../../assets/img/x.png) no-repeat top
            17px right 10px !important;
    }
    div.r {
        width: 897px;
        border-bottom: 1px solid #e5e5e5;
        height: 41px;
        line-height: 41px;
        // color: white;
        p.l {
            font-size: 12px;
            margin-left: 24px;
            input {
                float: left;
                margin: 14px 12px 0 0;
            }
        }
        p.r {
            margin-right: 23px;
            // color: white;
            span {
                // color: white;
                cursor: pointer;
                width: 48px;
                height: 20px;
                border: 1px solid #e6e6e6;
                float: left;
                margin-top: 9px;
                text-align: center;
                line-height: 20px;
                background: #fafafa;
                font-size: 11px;
                margin-right: 4px;
            }
        }
        .bg {
            color: #2d90dd;
            background: #fff !important;
        }
    }
}

.base {
    overflow: hidden;
    // height: 1200px;
    ul {
        li {
            width: 230px;
            // height: 420px;
            height: auto;
            border: 1px solid #f4f4f4;
            margin: 5px;
            box-sizing: border-box;
            img {
                display: block;
                display: block;
                margin: 10px;
                width: 210px;
                height: 210px;
            }
            .jieshao {
                font-size: 12px;
                color: #484848;
                margin: 0 11px;
                line-height: 20px;
                cursor: pointer;
                display: -webkit-box;
                overflow: hidden;
                white-space: normal !important;
                text-overflow: ellipsis;
                word-wrap: break-word;
                -webkit-line-clamp: 2;
                -webkit-box-orient: vertical;
                &:hover {
                    color: red;
                }
            }
            .bottom {
                width: 218px;
                height: 54px;
                margin: 18px 5px 10px 5px;
                p:nth-of-type(1) {
                    font-size: 12px;
                    color: #b4b4b4;
                    margin-top: 13px;
                    margin-left: 13px;
                    span {
                        text-decoration: line-through;
                    }
                }
                p:nth-of-type(2) {
                    cursor: pointer;
                    margin-top: 12px;
                    margin-right: 9px;
                    width: 68px;
                    height: 30px;
                    background: #de414c;
                    text-align: center;
                    line-height: 30px;
                    border-radius: 3px;
                    a {
                        font-size: 12px;
                        color: #fff;
                    }
                }
                p:nth-of-type(3) {
                    font-size: 13px;
                    color: #e64242;
                    margin-left: 13px;
                    width: 120px;
                    overflow: hidden;
                    text-overflow: ellipsis;
                    white-space: nowrap;
                }
            }
            .shop-show{
              height: 23px;
              display: flex;
             .show-shopname{
                font-size: 12px;
                padding: 0 10px 0 10px;
              }
              .shop-btn{
                width: 7px;
                height: 7px;
                margin: 5.7px 0 0 0;
              }
            }
            .first-name{
                color: #ffffff;
                background-color: #de2d35;
                font-size: 12px;
                border-radius: 3px;
                padding: .8px 3px 1.5px 3px;
                margin: 0 0 0 10px;
            }
            &:hover {
                box-shadow: 2px 4px 6px #f4f4f4;
            }
        }
        li:nth-child(5n) {
            margin-right: 0;
        }
    }
}

.fenye {
    overflow: hidden;
    display: flex;
    justify-content: center;
    .fenyes {
        // margin: 40px 308px;
        overflow: hidden;
    }
    span {
        height: 30px;
        width: 74px;
        border: 1px solid #e6e6e6;
        line-height: 30px;
        text-align: center;
        font-size: 12px;
        color: #838383;
    }
    ul {
        border: 1px solid #e6e6e6;
        margin: 0 10px;
    }
    li {
        width: 30px;
        height: 28px;
        line-height: 28px;
        text-align: center;
        border-right: 1px solid #e6e6e6;
        a {
            color: #838383;
        }
    }
    li:last-child {
        border: 0;
    }
    div {
        line-height: 30px;
        color: #838383;
        font-size: 12px;
        margin-bottom: 20px;
    }
    .gong {
        margin: 0 17px;
    }
    .go {
        input {
            width: 50px;
            height: 28px;
            border: 1px solid #e6e6e6;
        }
    }
}
</style>