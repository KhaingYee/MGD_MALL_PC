<template>
    <div class="withdraw-alaccount l">
        <div class="top">
            <span class="l">我的提现账户</span>
        </div>
        <div class="shixiang">
            <p>我的提现账户注意事项</p>
            <span>. 您需要先绑定您的银行卡、支付宝等账户才能申请账户提现</span
            ><span>. 申请提现时，网站方会将款项打入您选定的已绑定账户中</span
            ><span
                >.
                为确保您的账户信息安全，在绑定账户时需要进行身份验证，感谢您的理解和支持</span
            >
        </div>
        <el-table :data="myTableData" style="width: 100%">
            <el-table-column prop="user_id" label="编号" width="120">
            </el-table-column>
            <el-table-column prop="realname" label="姓名" width="120">
            </el-table-column>
            <el-table-column prop="id_card" label="身份证编号">
            </el-table-column>
            <el-table-column prop="belong" label="银行卡类型" width="120">
            </el-table-column>
            <el-table-column prop="card_num" label="银行卡编号" width="120">
            </el-table-column>
            <el-table-column prop="mobile" label="电话号码" width="120">
            </el-table-column>
            <el-table-column label="操作">
                <template slot-scope="scope">
                    <el-button size="mini" @click="handleUpdate(scope.row)"
                        >修改</el-button
                    >
                    <el-button size="mini" @click="handleDelete(scope.row)"
                        >删除</el-button
                    >
                </template>
            </el-table-column>
        </el-table>
    </div>
</template>
<script>
export default {
    data() {
        return {
            myTableData: [],
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
        this.getMyData();
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
        getMyData() {
            this.HTTP(this.$httpConfig.myWithdrawAccount, {}, "post")
                .then(res => {
                    this.myTableData = res.data.data;
                })
                .catch(err => {
                    console.error(err);
                });
        },
        // handleUpdate(item) {
      
        //     this.$router.push({
        //         name: "updateMyAccount",
        //         query:{
        //             id:item.id
        //         }
        //     });
        // },
        handleDelete(item) {
            this.$confirm("此操作将永久删除该条数据, 是否继续?", "提示", {
                confirmButtonText: "确定",
                cancelButtonText: "取消",
                type: "warning"
            })
                .then(() => {
                    this.HTTP(
                        this.$httpConfig.deleteWithdrawAccount,
                        {
                            id: item.id
                        },
                        "post"
                    )
                        .then(res => {
                        })
                        .catch(err => {
                            console.error(err);
                        });
                })
                .catch(() => {
                    this.$message({
                        type: "info",
                        message: "已取消删除"
                    });
                });
        }
    }
};
</script>
<style lang="less" scoped>
.t {
    padding: 5px;
    cursor: pointer;
}
.d {
    display: none;
}
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
}

.withdraw-alaccount {
    height: 956px;
    width: 980px;
    background: #fff;
    margin: 16px 0;
    .top {
        overflow: hidden;
        margin: 14px 17px 20px;
        border-bottom: 1px solid #e7e7e7;
        line-height: 37px;
        span.l {
            width: 104px;
            text-align: center;
            border-bottom: 2px solid #d02629;
            font-size: 14px;
            color: #d02629;
        }
        span.r {
            width: 80px;
            height: 28px;
            text-align: center;
            line-height: 28px;
            background: #d02629;
            color: #fff;
            border-radius: 4px;
            margin-top: 4px;
        }
    }
    .shixiang {
        margin: 0 17px 12px;
        border: 1px solid #bce8f1;
        background: #eff8ff;
        height: 141px;
        p {
            font-size: 15px;
            color: #333;
            margin: 15px 0 9px 23px;
        }
        span {
            font-size: 12px;
            color: #a5a7a8;
            line-height: 24px;
            display: block;
            margin-left: 23px;
        }
    }
    .under {
        overflow: hidden;
        border: 1px solid #e7e6e6;
        margin: 0 17px;
        .thead {
            line-height: 42px;
            background: #f1f1f1;
            overflow: hidden;
        }
        p {
            height: 100%;
            text-align: center;
            float: left;
            font-size: 12px;
            color: #333;
        }
        p:nth-of-type(1) {
            width: 212px;
        }
        p:nth-of-type(2) {
            width: 118px;
        }
        p:nth-of-type(3) {
            width: 154px;
            margin: 0 21px 0 28px;
        }
        p:nth-of-type(4) {
            width: 253px;
        }
        p:nth-of-type(5) {
            width: 81px;
        }
        .down {
            line-height: 39px;
            height: 39px;
            border-top: 1px solid #e7e6e6;
            span {
                color: #e31939;
            }
        }
    }
}
</style>
