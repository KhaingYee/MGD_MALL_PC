<template>
    <div class="vip-wrapper">
        <!-- <common-header></common-header> -->
        <common-header v-on:childToParent="onChildClick"></common-header>
        <store-header :allShop="true"></store-header>
        <div class="wrapper-hold">
            <div class="header">
                <h1>vip 会员说明</h1>
            </div>
            <div class="contailer">
                <img src="../../../assets/img/light.png" alt />
                <div class="right">
                    <p class="first">温馨提示</p>
                    <p class="second">
                        <span>会员等级按照在该店铺消费的总金额自动升级</span>
                    </p>
                </div>
            </div>
            <el-table :data="tableData" style="width: 100%">
                <el-table-column
                    prop="level_name"
                    label="会员等级"
                    width="180"
                ></el-table-column>
                <el-table-column prop="discount" label="折扣力度" width="180">
                    <template slot-scope="scope">
                        <span>{{ parseInt(scope.row.discount) + "%" }}</span>
                    </template>
                </el-table-column>
                <el-table-column
                    prop="money_big"
                    label="最高消费金额"
                ></el-table-column>
                <el-table-column
                    prop="money_small"
                    label="最低消费金额"
                ></el-table-column>
            </el-table>
            <div class="spec">*如有问题，请咨询店内客服</div>
        </div>
    </div>
</template>

<script>
export default {
    data() {
        return {
            tableData: [],
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
    created() {
      this.getFootData();
      this.getFavIcon(); 
    },
    mounted() {
        this.getVipInfo();       
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
                    let title = this.$route.query.class_name + '-' + sessionStorage.getItem('titleKey') + '-' +sessionStorage.getItem('descriptionStore');
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
        getVipInfo() {
            this.HTTP(
                this.$httpConfig.memberImportant,
                {
                    store_id: this.$route.query.id
                },
                "post"
            )
                .then(res => {
                    this.tableData = res.data.data;
                })
                .catch(err => {
                    console.error(err);
                });
        }
    }
};
</script>

<style lang="less" scoped>
.vip-wrapper {
    background-color: #fff;
    margin: 0 auto;

    .wrapper-hold {
        position: relative;
        margin: 0 15%;
        .header {
            margin: 20px;
            border-bottom: 1px solid #e7e7e7;
            padding-bottom: 10px;
            width: 800px;

            h1 {
                color: #d19e29;
                font-size: 16px;
                // border-bottom: 2px solid #d19e29
            }
        }

        .contailer {
            background: #eff8ff;
            display: flex;
            margin: 0 15px;

            img {
                margin: 30px;
                height: 28px;
            }

            .right {
                margin-top: 30px;
                margin-bottom: 30px;

                .first {
                    font-weight: bold;
                    font-size: 14px;
                    margin-bottom: 10px;
                }

                .second {
                    font-weight: bold;

                    span {
                        color: #9a9a9a;
                    }
                }

                .last {
                    font-weight: bold;

                    span {
                        color: #9a9a9a;
                    }
                }
            }
        }

        .spec {
            color: #dec2af;
            margin: 10px;
        }
    }
}
</style>
