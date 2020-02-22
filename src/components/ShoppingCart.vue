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
            <div class="shop-title">
              <div>
                <img src="../../static/images/shopitem.png" style="height: 15px; margin-bottom: 3px">
                <span class="name" >{{item.shopName}}</span>
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
                     @click="goodsCheck($event,item.detailLists,cartShops,item.productShopId)"
                     class="disN">
              <b></b>
            </label>
            <div class="middle">
              <img src="../../static/images/good.png" id="good-pic">
              <div>
                <p class="good-name">{{goods.name}}</p>
                <p class="good-type">{{goods.type}}</p>
                <p calss="tab">
                  <img src="../../static/images/goodDe.png" id="tab">
                </p>
              </div>
            </div>
            <div class="right">
              <p class="price"><b>￥{{goods.price}}</b></p>
              <p class="num">X{{goods.number}}</p>
              <p class="caculate">
                <span class="mark" @click="numDecrease(goods.number)" id="decrease">-</span>
                <span class="beeforCacul">{{goods.number}}</span>
                <span class="mark" @click="numAdd(goods.number)" id="add">+</span>
              </p>
            </div>
          </div>
        </div>
        <div class="edit" v-if="cartStatus === 'edit'">
          <label>
            <input type="checkbox" name="allRadio" class="disN" @click="allCheck($event)">
            <span class="select-all">全选</span>
          </label>
          <span class="delet">删除(3)</span>
        </div>
        <div class="gotopay" v-if="cartStatus === 'account'">
          <label>
            <input type="checkbox" name="allRadio" class="disN" @click="allCheck($event)">
            <span class="select-all">全选</span>
            <div class="pay">
              <span>合计：</span>
              <span class="sum">￥<b>{{sumPrice.toFixed(2)}}</b></span>
            </div>
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
        cartStatus:'account',  //购物车状态，account结算，edit删除编辑状态
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
        //商品选择
        goodsCheck(event,goodsList,shopList,shopId){
          var input = document.getElementsByTagName('input')
          if(event.currentTarget.checked){
            this.goodsList.push(String(event.currentTarget.value))
            //商品全选，店铺选中
            var newArr = this.goodsList
            var tt = goodsList.every(function(itemValue){
              return (newArr.indexOf(String(itemValue.detailId)))
            })
            if(tt){
              for(var i=0;i<input.length;i++){
                if(input[i].value == shopId){
                  input[i].checked = true
                }
              }
              this.shopList.push(String(shopId))
              if(this.shopList,length === shopList.length){
                for(var i=0;i<input.length;i++){
                  if(input[i].name == 'allRadio'){
                    input[i].checked = true
                  }
                }
              }
            }
          }
          else{
            this.goodsList.splice(this.goodsList.indexOf(event.currentTarget.value),1)
            for(var i=0;i<input.length;i++){
              if(input[i].value == shopId){
                if(input[i].checked){
                  input[i].checked = false
                  this.shopList.splice(this.shopList.indexOf(String(shopId)),1)
                }
              }
              if(input[i].name == 'allRadio'){
                input[i].checked = false
              }
            }
          }
          //计算总价和数量
          this.caculate()
        },
        //店铺选择
        shopCheck(event,shopList){
            //店铺选中则对应商品全选，否则全不选
            var input = document.getElementsByTagName('input')
            if(event.currentTarget.checked){
                this.shopList.push(String(event.currentTarget.value));
                for(var i = 0;i<input.length;i++){
                    if(input[i].getAttribute('dataId') == event.currentTarget.value){
                        if(!input[i].checked){
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
                        this.goodsList.splice(this.goodsList.indexOf(input[i].value),1);
                    }
                    if(input[i].name == 'allRadio'){
                        input[i].checked = false;
                    }
                }
            }
            //计算总价和数量
            this.caculate();
        },
        //全选
        allCheck(event){
          var input = document.getElementsByTagName('input')
            if(event.currentTarget.checked){
                //全选checked,所有店铺checked
                for(var i = 0;i<input.length;i++){
                    if(!input[i].checked){
                        input[i].checked = true;
                        if(input[i].name == 'shopRadio'){
                            this.shopList.push(String(input[i].value))
                        }
                        if(input[i].name == 'goodRadio'){
                            this.goodsList.push(String(input[i].value))
                        }
                    }
                }
            }else{
                //全不选取消checked，清空列表
                for(var i = 0;i<input.length;i++){
                    input[i].checked = false;
                    this.shopList = [];
                    this.goodsList = [];
                }
            }
            //计算总价和数量
            this.caculate();
        },
        caculate(){
           var input = document.getElementsByTagName('input');
            var newArr = []; 
            for(var i = 0;i<input.length;i++){
                if(input[i].name == 'goodRadio' && input[i].checked){
                    var num = input[i].parentNode.parentNode.children[2].children[2].children[2].innerHTML;
                    newArr.push(
                        {
                            'price': input[i].getAttribute('price'),
                            'num': num
                        }
                    )
                }
            }
            this.totalNumber = newArr.length;
            //归零
            this.sumPrice = 0;
            for(var j = 0,len = newArr.length;j<len;j++){
                this.sumPrice += newArr[j].price * newArr[j].num;
            }
        },
        //数量减小
        numDecrease(num){
            //减小的前提为input是否被选中
            var spanList = event.currentTarget.parentNode.children;
            for(var i = 0,len = spanList.length;i<len;i++){
                if(spanList[i].getAttribute("class") == 'cacul'){
                    spanList[i].style.display = 'block';
                    var caculNum = spanList[i].innerHTML;
                    if(caculNum < 2){
                        alert('宝贝不能再少了哦');
                    }else{
                        caculNum --
                        spanList[i].innerHTML = caculNum;
                    }
                }
            }
            if(event.currentTarget.parentNode.parentNode.parentNode.children[0].children[0].checked){
                this.caculate();
            }
        },
        //数量增加
        numAdd(num){
           var spanList = event.currentTarget.parentNode.children;
            for(var i = 0,len = spanList.length;i<len;i++){
                if(spanList[i].getAttribute("class") == 'cacul'){
                    spanList[i].style.display = 'block';
                    var caculNum = spanList[i].innerHTML;
                    caculNum ++;
                    spanList[i].innerHTML = caculNum;
                }
            }
            if(event.currentTarget.parentNode.parentNode.parentNode.children[0].children[0].checked){
                this.caculate();
            }
        },
        //结算
        cauSum(){
           if(this.sumPrice === 0){
                alert('您还没有选择宝贝哦')
            }else{
                alert('正在前往结算')
            }
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
    position: relative;
    display: block;
  }
  .shop-name{
    position: relative;
    height: 40px;
    width: 99.8%;
    border: #d1cfd3 solid 1px;
    color: gray;
  }
  .shop-name div{
    display: inline-block;
    left: 30px;
  }
  .shop-title{
    position: absolute;
    display: inline-block;
    left: 0px;
  }
  .disN{
    float: left;
    margin-top: 8px;
  }
  .good-list{
    position: relative;
    height: 90px;
    width: 99.9%;
    border: #d1cfd3 solid 1px;
    background-color: #e7e5e9;
  }
  .good-list div{
    display: inline-block;
  }
  .middle p{
    display: inline-block;
    margin-left: 10px
  }
  .middle{
    position: absolute;
    display: inline-block;
    left:30px;
  }
  #good-pic{
    position: absolute;
    top: 10px
  }
  .good-name{
    position: absolute;
    left:130px;
    width: 50px;
    margin-top: 35px;
  }
  .good-type{
    position: absolute;
    left:230px;
    width: 100px;
    height:30px;
    margin-top: 30px;
    font-size: .8em;
    color: gray;
    background-color:white;
    border-radius: 2px;
  }
  #tab{
    position: absolute;
    left: 380px;
    width: 50px;
    margin-top: 15px;
  }
  .right{
    position: absolute;
    right: 50px;
    top: 12px;
  }
  .right p{
    display: inline-block;
    margin-right: 30px;
  }
  .price{
    color: #e9691b;
    font-size: 1.2em;
  }
  .num{
    background-color: white;
    width: 40px;
    height:20px;
    border-radius: 3px;
    color: gray;
    font-size: .9em;
  }
  .caculate{
    background-color: white;
    width:80px;
    height:25px;
    border: solid 1px rgb(184, 184, 184);
  }
  .mark{
    position: absolute;
    width:25px; 
    font-size: 1.2em;
    color:gray;
    border: solid 1px rgb(184, 184, 184);
  }
  .mark:hover{
    cursor: pointer;
    background-color:#e7e5e9;
  }
  #decrease{
    position: absolute;
    right: 83px;
  }
  #add{
    position: absolute;
    right: 31px;
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
   .select-all{
     position: absolute;
     left: 25px;
     top: 5px;
     color: gray;
   }
   .pay{
     position:absolute;
     right: 120px;
     top:10px;
   }
   .sum{
     font-size: 1.2em;
     color: #e9691b
   }
   .delet{
     position: absolute;
     right: 0;
     background-color: #e9691b;
     color:white;
     top:0;
     height:100%;
     width:100px;
     line-height: 42px;
   }
   .delet:hover{
     cursor: pointer;
   }
</style>
