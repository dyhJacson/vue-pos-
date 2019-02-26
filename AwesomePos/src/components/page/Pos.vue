<template>
  <div class="pos">
    <el-row>
      <el-col :span='7'
              class="pos-order"
              id='order-list'>
        <el-tabs>
          <el-tab-pane label="点餐">
            <el-table :data='tableDate'
                      border
                      style="100%">
              <el-table-column prop='goodsName'
                               label="商品名称">
              </el-table-column>
              <el-table-column prop='count'
                               label="量"
                               width="50">
              </el-table-column>
              <el-table-column prop='price'
                               label="金额"
                               width="70">
              </el-table-column>
              <el-table-column label="操作"
                               width="100"
                               fixed="right">
                <template>
                  <el-button type="text"
                             size='small'>删除</el-button>
                  <el-button type="text"
                             size='small'>增加</el-button>
                </template>
              </el-table-column>
            </el-table>
            <div class="totalDiv">
              <small>数量：</small>{{this.totalCount}} &nbsp;&nbsp;&nbsp;&nbsp; <small>金额：</small>{{this.totalMoney}}元

            </div>
            <div class="div-btn">
              <el-button type="warning">挂单</el-button>
              <el-button type="danger">删除</el-button>
              <el-button type="success"
                         @click="checkout ()">结账</el-button>
            </div>
          </el-tab-pane>
          <el-tab-pane label="挂单">
          </el-tab-pane>
          <el-tab-pane label="外卖">
          </el-tab-pane>
        </el-tabs>
      </el-col>
      <el-col :span="17">
        <div class="title">
          常用商品
        </div>
        <div class="often-goods-list">
          <ul>
            <li v-for="goods in oftenGoods"
                :key="goods.goodsname"
                @click="addOrderList(goods)">
              <span>{{goods.goodsName}}</span>
              <span class="o-price">￥{{goods.price}}</span>
            </li>
          </ul>
        </div>
        <div class="goods-type">
          <el-tabs class="eltabs">
            <el-tab-pane label="汉堡">
              <div>
                <ul class='cookList'>
                  <li v-for="goods in type0Goods"
                      :key="goods.goodsname"
                      @click="addOrderList(goods)">
                    <span class="foodImg"><img src="goods.goodsImg"
                           width="100%"></span>
                    <span class="foodName">{{goods.goodsName}}</span>
                    <span class="foodPrice">￥{{goods.price}}元</span>
                  </li>
                </ul>
              </div>
            </el-tab-pane>
            <el-tab-pane label="小食">
              <div>
                <ul class='cookList'>
                  <li v-for="goods in type1Goods"
                      :key="goods.goodsname"
                      @click="addOrderList(goods)">
                    <span class="foodImg"><img src="goods.goodsImg"
                           width="100%"></span>
                    <span class="foodName">{{goods.goodsName}}</span>
                    <span class="foodPrice">￥{{goods.price}}元</span>
                  </li>
                </ul>
              </div>
            </el-tab-pane>
            <el-tab-pane label="饮料">
              <div>
                <ul class='cookList'>
                  <li v-for="goods in type2Goods"
                      :key="goods.goodsname"
                      @click="addOrderList(goods)">
                    <span class="foodImg"><img src="goods.goodsImg"
                           width="100%"></span>
                    <span class="foodName">{{goods.goodsName}}</span>
                    <span class="foodPrice">￥{{goods.price}}元</span>
                  </li>
                </ul>
              </div>
            </el-tab-pane>
            <el-tab-pane label="套餐">
              <div>
                <ul class='cookList'>
                  <li v-for="goods in type3Goods"
                      :key="goods.goodsname"
                      @click="addOrderList(goods)">
                    <span class="foodImg"><img src="goods.goodsImg"
                           width="100%"></span>
                    <span class="foodName">{{goods.goodsName}}</span>
                    <span class="foodPrice">￥{{goods.price}}元</span>
                  </li>
                </ul>
              </div>
            </el-tab-pane>
          </el-tabs>

        </div>
      </el-col>
    </el-row>

  </div>
</template>

<script>
import axios from 'axios'
export default {
  name: 'pos',
  data () {
    return {
      tableDate: [],
      oftenGoods: [],
      type0Goods: [],
      type1Goods: [],
      type2Goods: [],
      type3Goods: []
    }
  },
  created () {
    axios.get('https://www.easy-mock.com/mock/5b8b30dbf032f03c5e71de7f/kuaican/oftenGoods')
      .then(response => { console.log(response); this.oftenGoods = response.data })
      .catch(error => {
        console.log(error)
        alert('网络错误，不能访问')
      })
    // 读取分类商品列表
    axios.get('https://www.easy-mock.com/mock/5b8b30dbf032f03c5e71de7f/kuaican/typeGoods')
      .then(response => {
        console.log(response)
        // this.oftenGoods=response.data;
        this.type0Goods = response.data[0]
        this.type1Goods = response.data[1]
        this.type2Goods = response.data[2]
        this.type3Goods = response.data[3]
      })
      .catch(error => {
        console.log(error)
        alert('网络错误，不能访问')
      })
  },
  mounted () {
    var orderHeight = document.body.clientHeight
    document.getElementById('order-list').style.height = orderHeight + 'px'
  },
  methods: {
    addOrderList (goods) {
      // 判断商品是否已经存在订单列表中
      // 根据判断的值再写业务逻辑
      this.totalCount = 0 // 汇总数量清0
      this.totalMoney = 0
      let isHave = false
      for (let i = 0; i < this.tableDate.length; i++) {
        if (this.tableDate[i].goodsId === goods.goodsId) {
          isHave = true
        }
      }
      // 根据isHave的值判断订单列表中是否已经有此商品
      if (isHave) {
        // 存在就进行数据添加
        let arr = this.tableDate.filter(o => o.goodsId === goods.goodsId)
        arr[0].count++
      } else {
        // 不存在就推如数据
        let newGoods = { goodsId: goods.goodsId, goodsName: goods.goodsName, price: goods.price, count: 1 }
        this.tableDate.push(newGoods)
      }
      // 进行数量和价格的汇总计算
      this.tableDate.forEach((element) => {
        this.totalCount += element.count
        this.totalMoney = this.totalMoney + (element.price * element.count)
      })
    },
    delAllGoods () {
      this.tableDate = []
      this.totalCount = 0
      this.totalMoney = 0
    },
    // 模拟结账
    checkout () {
      if (this.totalCount !== 0) {
        this.tableDate = []
        this.totalCount = 0
        this.totalMoney = 0
        this.$message({
          message: '结账成功',
          type: 'success'
        })
      } else {
        this.$message.error('不能空结账')
      }
    }
  }
}
</script>
<style>
.pos-order {
  background-color: #f9fafc;
  border-right: 1px solid #c0ccda;
}
.div-btn {
  margin-top: 10px;
}
.title {
  height: 20px;
  border-bottom: 1px solid #d3dce6;
  background-color: #f9fafc;
  padding: 10px;
  text-align: left;
}
.often-goods-list ul li {
  list-style: none;
  float: left;
  border: 1px solid #e5e9f2;
  padding: 10px;
  margin: 10px;
  background-color: #fff;
  cursor: pointer;
}
.goods-type {
  clear: both;
}
.eltabs {
  padding: 5px;
}
.cookList li {
  list-style: none;
  width: 27%;
  border: 1px solid #e5e9f2;
  height: auot;
  overflow: hidden;
  background-color: #fff;
  padding: 2px;
  float: left;
  margin: 2px;
  cursor: pointer;
}
.cookList li span {
  display: block;
  float: left;
}
.foodImg {
  width: 40%;
}
.foodName {
  font-size: 18px;
  padding-left: 10px;
  color: brown;
}
.foodPrice {
  font-size: 16px;
  padding-left: 10px;
  padding-top: 10px;
}
.totalDiv {
  background-color: #fff;
  padding: 10px;
  border-bottom: 1px solid #d3dec6;
}
</style>
