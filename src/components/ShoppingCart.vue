<template>
    <div class="list-container">
        <div class="top">
          <div>
            <img src="../../static/images/all.png">
          </div>
          <div class="cartStatus">
            <span v-if="cartStatus === 'account'" @click="cartStatus = 'edit'"><b>编辑商品</b></span>
            <span v-if="cartStatus === 'edit'" @click="cartStatus = 'account'"><b>完成</b></span>
          </div>
        </div>
      <div class="lists">
        <div class="list-item" v-for="(item,index) in cartShops" :key="index">
          <div class="shop-name">
            <label>
              <input type="checkbox"
                     name="shopRadio"
                     :value="item.shopId"
                     @click="shopCheck($event,cartShops)"
                     class="disN">
              <b></b>
            </label>
            <div>
              <div>
                <img src="../../static/images/shopitem.png" style="height: 15px; margin-top: 7px">
                <span class="name">{{item.shopName}}</span>
              </div>
              <span>
                <img src="../../static/images/shopName.png" style="height: 25px;margin-top: 3px;margin-left: 10px">
              </span>
            </div>
          </div>
          <div class="good-list" v-for="(goods,goodsIndex) in item.detailLists" :key="goodsIndex">
            <label>
              <input type="checkbox"
                     name="goodRadio"
                     :price="goods.price"
                     :num="goods.number" :dataId="item.shopId"
                     :value="goods.detailId"
                     @click="goodsCkeck($event,item.detailLists,cartShops,item.productShopId)"
                     class="disN">
              <b></b>
            </label>
            <div class="middle">
              <img src="../../static/images/good.png">
              <div>
                <p class="good-name">{{goods.name}}</p>
                <p class="good-type">{{goods.type}}</p>
                <p calss="tab">
                  <img src="../../static/images/goodDe.png">
                </p>
              </div>
            </div>
            <div class="right">
              <p class="price"><b>￥{{goods.price}}</b></p>
              <p class="num">X{{goods.number}}</p>
              <p class="caculate">
                <span class="mark" @click="numDecrease(goods.number)"></span>
                <span class="beeforCacul">{{goods.number}}</span>
                <span class="cacul" :num="goods.cartDetailId">{{goods.number}}</span>
                <span class="mark" @click="numAdd(goods.number)"></span>
              </p>
            </div>
          </div>
        </div>
        <div class="edit" v-if="cartStatus === 'edit'">
          <label>
            <input type="checkbox" name="allRadio" class="disN" @click="allCheck($event)">
            <b></b>
            <span>全选</span>
          </label>
          <span class="delet">删除(3)</span>
        </div>
        <div class="gotopay" v-if="cartStatus === 'account'">
          <label>
            <input type="checkbox" name="allRadio" class="disN" @click="allCheck($event)">
            <b></b>
            <span class="marginR40">全选</span>
            <span>合计：</span>
            <span class="sum">￥{{sumPrice.toFixed(2)}}</span>
          </label>
          <span class="delet" @click="cauSum">结算({{totalNumber}})</span>
        </div>
      </div>
    </div>
</template>

<script>
export default {
  name: 'ShoppingCart',
  data () {
    return {
        cartStatus:"account",  //购物车状态，account结算，edit删除编辑状态
        cartShops: [
            {shopId:0,shopName:"店铺1",detailLists:[
                    {goodsIndex:0,price:20,number:1,detailId:0,name:"商品1",type:"颜色分类：黑"},
                    {goodsIndex:1,price:30,number:2,detailId:1,name:"商品2",type:"颜色分类：黑"},
                ]},
            {shopId:1,shopName:"店铺2",detailLists:[
                    {goodsIndex:0,price:20,number:1,detailId:0,name:"商品1",type:"颜色分类：黑"}
                ]},
        ],  //店铺列表
        sumPrice:0,  //合计金额
        totalNumber: 0, //总数
        shopList:[],  //店铺列表
        goodsList:[],  //商品列表
    }
  },
    methods:{
        //店铺选择
        shopCheck(event,shopList){
            //店铺选中则对应商品全选，否则全不选
            var input = document.getElementsByTagName('input')
            if(event.currentTarget.checked){
                //店铺列表+，店铺checked,店铺内商品全checked，商品列表++
                this.shopList.push(String(event.currentTarget.value));
                //店铺内商品全checked
                for(var i = 0;i<input.length;i++){
                    if(input[i].getAttribute('dataId') == event.currentTarget.value){
                        //将没有选中的checked,并加入列表，去重
                        if(!input[i].checked){
                            input[i].checked = true;
                            //商品列表++
                            this.goodsList.push(String(input[i].value))
                        }
                    }
                }
                //所有店铺全选
                if(this.shopList.length === shopList.length){
                    for(var i = 0;i<input.length;i++){
                        if(input[i].name == 'allRadio'){
                            input[i].checked = true;
                        }
                    }
                }
            }else{
                //店铺取消checked,店铺列表--，店铺内所有商品取消checked,商品列表--
                this.shopList.splice(this.shopList.indexOf(String(event.currentTarget.value)),1);
                //店铺内所有商品取消checked
                for(var i = 0;i<input.length;i++){
                    if(input[i].getAttribute('dataId') == event.currentTarget.value){
                        input[i].checked = false;
                        //商品列表--
                        this.goodsList.splice(this.goodsList.indexOf(input[i].value),1);
                    }
                    //任意一个不选，全选取消
                    if(input[i].name == 'allRadio'){
                        input[i].checked = false;
                    }
                }
            }
            //计算总价和数量
            this.caculate();
        }
    }
}
</script>

<style scoped>
  .list-container{
    position: fixed;
    background-color: white;
    height: 80%;
    width: 90%;
    top: 0;bottom: 0;left: 0;right: 0;
    margin: auto;
  }
  .top{
    position: relative;
    height: 8%;
    border: #d1cfd3 solid 1px;
  }
  .top img{
    position: absolute;
    display: inline;
    margin: 2px;
    left: 0;
  }
  .cartStatus{
    position: absolute;
    display: inline;
    margin: 2px;
    top: 15%;
    right: 20px;
    color: #e9691b;
    font-size: 1.3em;
  }
  .cartStatus:hover{
    cursor: pointer;
  }
  .list-item{
  }
  .shop-name{
    height: 5%;
    width: 99.8%;
    border: #d1cfd3 solid 1px;
  }
  .shop-name div{
    display: inline-block;
  }
  .disN{
    float: left;
    margin-top: 8px;
  }
  .good-list{
    height: 20%;
    width: 99.9%;
    border: #d1cfd3 solid 1px;
    background-color: #e7e5e9;
  }
  .good-list div{
    display: inline-block;
  }
  .middle p{
    display: inline-block;
  }
  .right p{
    display: inline-block;
  }
  .gotopay{
    position: absolute;
    bottom: 0;
    width: 99.9%;
    height: 7%;
    border: #d1cfd3 solid 1px;
  }
  .edit{
    position: absolute;
    bottom: 0;
    width: 99.9%;
    height: 7%;
    border: #d1cfd3 solid 1px;
  }
  .price{
    color: #e9691b;
  }
</style>
