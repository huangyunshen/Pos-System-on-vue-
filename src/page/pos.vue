<template>
    <div class="pos">
        <div class="pos-order">
          <el-tabs>
            <el-tab-pane label="点餐">
              <el-table
                border
                :data="tableData"
                stripe
              >
                <el-table-column type="index" label="序号" width="50"></el-table-column>
                <el-table-column prop="goodsName" label="商品名称">

                </el-table-column>
                <el-table-column prop="count" label="数量" width="50">

                </el-table-column>
                <el-table-column prop="price" label="单价" width="70">

                </el-table-column>
                <el-table-column label="操作" fixed="right" width="130">
                  <template scope="scope">
                    <el-button type="text" size="small" @click="countCtrl(scope.row,'reduce')">
                      减少
                    </el-button>
                    <el-button type="text" size="small" @click="countCtrl(scope.row,'add')">
                      增加
                    </el-button>
                    <el-button type="text" size="small" @click="deleteOrderList(scope.row)" :plain="true">
                      删除
                    </el-button>
                  </template>
                </el-table-column>
              </el-table>
              <div class="totals">
                <span>数量：</span>
                <span class="total-style">{{totalCount}}</span>
                &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
                <span>总价：</span>
                <span class="total-style">￥{{totalPrice}}元</span>
              </div>
            </el-tab-pane>

            <el-tab-pane label="挂单">
              挂单
            </el-tab-pane>

            <el-tab-pane label="外卖">
              外卖
            </el-tab-pane>
          </el-tabs>

          <div class="btn-groups">
            <el-button type="warning">挂单</el-button>
            <el-button type="danger" @click="clearAll">清空</el-button>
            <el-button type="success" @click="clearAll">结账</el-button>
          </div>
        </div>

        <div class="right-content">
          <div class="often-goods">
            <div class="often-title">常用商品</div>
            <div class="often-list">
              <ul>
                <li v-for="(item,index) in oftenGoods" class="noChoose" @click="addOrderList(item)">
                  <span>{{item.goodsName}}</span>
                  <span class="often-price">￥{{item.price}}元</span>
                </li>
              </ul>
            </div>
          </div>

          <div class="goods-type">
              <el-tabs>
                <el-tab-pane label="汉堡">
                  <div>
                    <ul class="food-list">
                      <li v-for="(item,index) in type0Goods" class="noChoose" @click="addOrderList(item)">
                        <span class="food-img"><img :src="item.goodsImg" width="100%"></span>
                        <span class="food-name">{{item.goodsName}}</span>
                        <span class="food-price">￥{{item.price}}元</span>
                      </li>
                    </ul>
                  </div>
                </el-tab-pane>
                <el-tab-pane label="小食">
                  <div>
                    <ul class="food-list">
                      <li v-for="(item,index) in type1Goods" class="noChoose" @click="addOrderList(item)">
                        <span class="food-img"><img :src="item.goodsImg" width="100%"></span>
                        <span class="food-name">{{item.goodsName}}</span>
                        <span class="food-price">￥{{item.price}}元</span>
                      </li>
                    </ul>
                  </div>
                </el-tab-pane>
                <el-tab-pane label="饮料">
                  <div>
                    <ul class="food-list">
                      <li v-for="(item,index) in type2Goods" class="noChoose" @click="addOrderList(item)">
                        <span class="food-img"><img :src="item.goodsImg" width="100%"></span>
                        <span class="food-name">{{item.goodsName}}</span>
                        <span class="food-price">￥{{item.price}}元</span>
                      </li>
                    </ul>
                  </div>
                </el-tab-pane>
                <el-tab-pane label="套餐">
                  <div>
                    <ul class="food-list">
                      <li v-for="(item,index) in type3Goods" class="noChoose" @click="addOrderList(item)">
                        <span class="food-img"><img :src="item.goodsImg" width="100%"></span>
                        <span class="food-name">{{item.goodsName}}</span>
                        <span class="food-price">￥{{item.price}}元</span>
                      </li>
                    </ul>
                  </div>
                </el-tab-pane>
              </el-tabs>
          </div>
        </div>
    </div>

</template>

<script>

  const deepClone=(obj)=>{
    let proto=Object.getPrototypeOf(obj);
    return Object.assign({},Object.create(proto),obj);
  };

  import axios from 'axios';
  export default {
    name: "pos",
    data(){
      return {
        tableData:[],
        totalCount:0,
        totalPrice:0,
        oftenGoods:[],
        type0Goods:[],
        type1Goods:[],
        type2Goods:[],
        type3Goods:[],
      }
    },
    created(){
      axios.get('http://jspang.com/DemoApi/oftenGoods.php')
        .then(res=>{
          if(res.status===200){
            this.oftenGoods=res.data;
          }
        })
        .catch(err=>{
          alert('网络错误');
        });
      axios.get('http://jspang.com/DemoApi/typeGoods.php')
        .then(res=>{
          if(res.status===200){
            this.type0Goods=res.data[0];
            this.type1Goods=res.data[1];
            this.type2Goods=res.data[2];
            this.type3Goods=res.data[3];
          }
        })
        .catch(err=>{
          alert('网络错误');
        })
    },
    methods:{
      addOrderList(goods){
        let flag=false;
        let index=null;
        for(let i=0;i<this.tableData.length;i++){
          if(goods.goodsId===this.tableData[i].goodsId){
            flag=true;
            index=i;
          }
        }

        if(flag){
          this.tableData[index].count++;
        } else {
          let obj=deepClone(goods);
          obj.count=1;
          this.tableData.push(obj);
        }
        this.totalsFun();
      },
      countCtrl(goods,flag){
        if(flag==='add'){
          goods.count++;
        } else {
          goods.count--;
          if(goods.count<1){
            goods.count=1;
          }
        }
        this.totalsFun();
      },
      totalsFun(){
        this.totalCount=0;
        this.totalPrice=0;
        this.tableData.forEach((v,k)=>{
        this.totalCount+=v.count;
        this.totalPrice+=v.price*v.count;
        })

      },

      deleteOrderList(goods){
        if(this.tableData.length){
          this.tableData=this.tableData.filter(o=>o.goodsId!==goods.goodsId);
          this.$message({
            message:'已删除！',
            type:'warning'
          });
          this.totalsFun();
        }
      },
      clearAll() {
        if (this.tableData.length) {
          this.$confirm('是否清空所有商品？', {
            confirmButtonText: '确定',
            cancelButtonText: '取消',
            type: 'warning'
          }).then(() => {
            this.tableData = [];
            this.totalCount = 0;
            this.totalPrice = 0;
            this.$message({
              type: 'success',
              message: '已清空'
            })
          });
        }
      }
    }
  };

</script>

<style scoped>
  .noChoose{
    -moz-user-select:none;
  }

  .pos{
    height: 100%;
  }
  .pos-order{
    background: #eee;
    border-right: 2px solid #1D8CE0;
    float: left;
    width: 30%;
    height: 100%;
    padding: 0 5px;
    -webkit-box-sizing: border-box;
    -moz-box-sizing: border-box;
    box-sizing: border-box;
  }
  .totals{
    font-size: 16px;
    padding: 15px;
  }
  .total-style{
    color: red;
    font-size: 20px;
  }
  .btn-groups{
    margin-top: 10px;
  }

  .right-content{
    float: left;
    width: 65%;
  }
  .often-title{
    height: 38px;
    font-size: 20px;
    font-weight: bold;
    line-height: 38px;
    border-bottom: 1px solid #ddd;
    background: #f9fafc;
  }
  .often-goods:after{
    content: '';
    clear:both;
    display: table;
  }
  .often-list ul li{
    float:left;
    border:1px solid #ddd;
    padding: 10px;
    margin:10px;
    background: #C9E8FF;
    -webkit-border-radius: 5px;
    -moz-border-radius: 5px;
    border-radius: 5px;
  }
  .often-list ul li:hover{
    cursor: pointer;
    background: #89C9FA;
  }
  .often-price{
    color: #FF3E3E;
  }

  .goods-type{
    padding: 10px;
  }

  .food-list li{
    width:200px;
    border:1px solid #E5E9F2;
    overflow: hidden;
    background-color:#fff;
    padding: 2px;
    padding-bottom: 0;
    float:left;
    margin: 5px;
  }
  .food-list li:hover{
    -webkit-box-shadow: 0 5px 10px rgba(0, 0, 0, 0.1);
    -moz-box-shadow: 0 5px 10px rgba(0, 0, 0, 0.1);
    box-shadow: 0 5px 10px rgba(0, 0, 0, 0.1);
    -webkit-transform: translate3d(0, -2px, 0);
    -moz-transform: translate3d(0, -2px, 0);
    -ms-transform: translate3d(0, -2px, 0);
    -o-transform: translate3d(0, -2px, 0);
    transform: translate3d(0, -2px, 0);
  }
  .food-list li span{
    display: block;
    float:left;
  }
  .food-img{
    width: 40%;
  }
  .food-name{
    font-size: 18px;
    padding-left: 10px;
    color:brown;

  }
  .food-price{
    font-size: 16px;
    padding-left: 10px;
    padding-top:10px;
  }

</style>
