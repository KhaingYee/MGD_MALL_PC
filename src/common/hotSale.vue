<template>
  <div class="hotSale">
    <div class="remai" v-show="hotList === null">
			<div class="left l">热<br />卖<br />推<br />荐</div>
			<div class="right l">
        <span class="nullData" >{{hotList.length === 0 ? '暂无数据' : ''}}</span>
				<ul>
					<li class="l" v-for="(item, index) in hotList" :key="index">
						<img :src="URL + item.pic_url" />
						<p>{{item.title}}</p>
						<p>特价：
							<span>{{item.member}}</span>
						</p>
						<p>查看详情</p>
					</li>
          <!-- shop_sn -->
				</ul>
			</div>
		</div>
  </div>
</template>
<script>
export default {
  data() {
    return {
      hotList: []
    };
  },
  created () {

    if(this.$route.query.type == 2){
      var data = {
        class_three: this.$route.query.class_id
      }
    }else {
      var data = {
        id: this.$route.query.class_id
      }
    }

    this.HTTP(this.$httpConfig.RecommendedSale, data , 'post').then(res => {
      if (res.data.status) this.hotList = res.data.data;
    }).catch(e=>{
          console.log(e)
    })
  }
};
</script>
<style lang="less" scoped>
.l {
  float: left;
}
.r {
  float: right;
}
.remai {
  height: 149px;
  .left {
    width: 38px;
    height: 100%;
    background: #d02629;
    text-align: center;
    padding-top: 40px;
    font-size: 14px;
    color: #fff;
  }
  .right {
    height: 100%;
    width: 1159px;
    background: #fafafa;
    margin-left: 3px;
    .nullData {
      display: flex;
      height: 100%;
      justify-content: center;
      align-items: center;
      font-size: 25px;
    }
    li {
      width: 274px;
      height: 120px;
      background: #fff;
      margin: 14px 0 0 12px;
      img {
        margin: 13px 9px 0 10px;
        float: left;
      }
      p {
        width: 128px;
        float: left;
        font-size: 12px;
        color: #555;
        cursor: pointer;
      }
      p:nth-of-type(1) {
        margin-top: 17px;
        &:hover {
          color: red;
        }
      }
      p:nth-of-type(2) {
        margin-top: 10px;
        span {
          color: #d02629;
        }
      }
      p:nth-of-type(3) {
        margin-top: 6px;
        width: 74px;
        height: 25px;
        background: #d02629;
        text-align: center;
        line-height: 25px;
        color: #fff;
      }
    }
  }
}
</style>

