<template>
    <div class="singal-data">
        <con-header></con-header>
        <div class="shop">
            <p>被举报商家</p>
            <span @click="goShopHome">{{ singalData.shop_name }}</span>
        </div>
        <div class="detail">
            <div class="top">
                <span>相关商品:</span>
                <span @click="goGoodsHome"> {{ singalData.title }}</span>
            </div>
        </div>
        <div class="type">
            <span>举报类型: </span>
            <div class="show-detail">
                {{ singalData.type }}
            </div>
        </div>
        <div class="select-option">
            <span>举报主题: </span>
            <div class="show-select">
                {{ singalData.topic }}
            </div>
        </div>
        <div class="content">
            <span>举报内容: </span>
            <div class="show-text">
                {{ singalData.content }}
            </div>
        </div>
        <div id="showPic">图片:</div>
        <div class="pic" v-for="(item, index) in handlePicUrl" :key="index">
            <img :src="imgUpload + item" alt="" />
        </div>

        <div class="notice">
            <div class="btn" @click="back">返回列表</div>
        </div>
    </div>
</template>

<script>
import ConHeader from "./comHeader";
export default {
    data() {
        return {
            imgUpload: "http://center.geekmall.plus"
        };
    },
    mounted() {
        let newData = JSON.parse(sessionStorage.singalKey);
    },

    computed: {
        handlePicUrl() {
            if (Object.prototype.toString.call(this.singalData.pic_url)=="[object String]") {
                return this.singalData.pic_url.split(",");
            } else {
                return this.singalData.pic_url;
            }
        }
    },
    props: {
        singalData: {
            type: Object,
            default: null
        }
    },
    methods: {
        back() {
            this.$emit("cancel");
        },
        goShopHome() {
            this.$emit("goShopHome",this.singalData.store_id);
        },
        goGoodsHome() {
            this.$emit("goGoodsHome",this.singalData.goods_id);
        }
    },
    components: {
        ConHeader
    }
};
</script>
<style lang="less">
#showPic {
    margin-left: 26px;
}
</style>
<style lang="less" scoped>
.singal-data {
    h6 {
        font-size: 17px;
        border-bottom: 1px solid #e3e3e3;
        margin: 16px;
        padding-bottom: 7px;
    }

    .shop {
        margin: 10px;
        background: #f6f6f6;
        padding: 20px;

        p {
            display: inline-block;
        }

        span {
            margin-left: 30px;
            cursor: pointer;

            &:hover {
                color: rgb(134, 163, 226);
            }
        }
    }

    .detail {
        .top {
            margin-left: 28px;
            span {
                &:nth-child(2) {
                    &:hover {
                        cursor: pointer;
                        color: rgb(134, 163, 226);
                    }
                }
            }
        }
    }

    .type {
        margin: 28px;

        .show-detail {
            display: inline-block;
        }

        .container-radio {
            margin-left: 65px;
            line-height: 48px;
            margin-top: -33px;
        }
    }

    .select-option {
        margin-left: 28px;

        .show-select {
            display: inline-block;
        }
    }

    .content {
        margin: 26px;

        .show-text {
            display: inline-block;
        }
    }

    .pic {
        margin: 25px;
        img {
            width: 200px;
            height: 100px;
            margin: 10px 0;
        }
    }

    .notice {
        margin: 25px;

        .btn {
            background: red;
            width: 100px;
            margin: 10px auto;
            text-align: center;
            border-radius: 5px;
            height: 36px;
            line-height: 36px;
            color: #fff;
            font-weight: bold;
            cursor: pointer;
        }
    }
}
</style>
