<template>
    <view class="bg-white " style="height: 100vh;">
        <view class="bg-blue  flex justify-between padding-xl padding-right-0" style="overflow:hidden;background-image: url(https://lft-ad.oss-cn-hangzhou.aliyuncs.com/eleme/png/bg-recharge-head_1001_2.png);background-size: 100% 100%;">
            <view class="space bg-black">
                <view class="star "></view>
                <view class="star pink "></view>
                <view class="star blue "></view>
                <view class="star yellow "></view>
            </view>
            <view>
                <view class="text-sm">当前粮票</view>
               <view class="flex align-end">
                   <view class="bg-cash-sm margin-bottom-xs"> </view>
                   <view class="text-xxl margin-left-sm text-bold" style="font-size: 50rpx;">{{userInfo.times}}</view>
               </view>
            </view>
            <view class="flex align-center">
                <view class="bg-white text-red  padding-xs text-sm" style="border-top-left-radius: 30px;border-bottom-left-radius: 30px;" @click="handleBack" >
                    <text class="cuIcon-back margin-right-xs margin-left-xs" style="" ></text>
                    返回首页
                </view>
            </view>
        </view>
        <view class="padding  margin-top">
            <view>请选择优惠套餐</view>
            <view class="flex justify-between  margin-sm margin-top-xl" v-for="(item,index) in recharge.list"  :key="index" >
                <view :style="'background-image:url('+item2.bg+');background-size: 100% 100%;' + (item2.color ? 'border-color:'+ item2.color + ';' : '') " v-for="(item2,index) in item" :key="index" class="flex-sub border-3px bg-gold   padding-top padding-bottom  radius-xxxl margin-right money-item" hover-class="money-item-choose" @click="handleRecharge(item2)">
                    <view class="flex justify-center align-end">
                        <view class="text-shadow text-money  text-red text-bold">{{item2.coin}}</view>
                        <view class="text-red text-md margin-bottom-xs margin-left-xs">粮票</view>
                    </view>
                    <view class="flex justify-center margin-top-xs">
                        <view class="left-tag bg-china-red">￥{{item2.pay_price}}</view>
                        <view class="right-tag bg-china-red" style="text-decoration: line-through;"> 原价:{{item2.price!== '' ? item2.price : pay_price + 1}}</view>
                    </view>
                    <view class="my-badge bg-china-red " >{{item2.name}}</view>
                </view>



            </view>
        </view>
    </view>
</template>

<script>
    import {createAndPaymentOrder, createOrder, getRechargeList, pay} from "../../api/orders";
    import {navigateTo} from "../../utils/utils";
    import cache from "../../utils/cache";

    export default {
        name: "recharge",
        data(){
            return{
               cardInfo:{},
               recharge:{},
                userInfo:cache.get("userInfo")

            }
        },
        async onLoad(options) {
            this.cardInfo =options;
            console.log(`调试:接收到参数`, options);
            this.recharge = await  getRechargeList();
            this.recharge.list = [];
            for(let i=0;i<this.recharge.items.length;i+=2){
                this.recharge.list.push(this.recharge.items.slice(i,i+2))
            }
            console.log(`调试:recharge`,  this.recharge )
        },
        methods:{
           async handleBack(){
               uni.navigateBack(-1);
               uni.reLaunch({
                   url:'../user/user'
               })
           },
            async handleRecharge(item){
              console.log(`调试:你选择了充值套餐`, item);
              await pay({repid:item.id});

            },
           async handleChoose(e){
                let { rechargeyuan } = e;

               let result = await createAndPaymentOrder({
                    price:rechargeyuan,
                    cardno:this.cardInfo.cardno
                });
               let { outtradeno  } = result;
               console.log(`调试:支付结果`, result);
               navigateTo({
                   url:'../waitResult/waitResult',
                   params:{
                       outtradeno,
                       ...this.cardInfo
                   }
               });




            }
        }
    }
</script>

<style scoped>
    @import "star.css";
    .bg-cash-sm{
        background-image: url("../../static/cash_yellow.png");
        background-size: 100% 100%;
        width: 60rpx;
        height: 35rpx;
        display: inline-block;
    }
    .text-shadow{
        text-shadow: 2px 2px 1px #80808059;
    }
    .text-money{
        font-size:60rpx;
    }
    .left-tag{
        /*background:#fff8ee;*/
        /*color: #d3a55e;*/
        font-size: 10px;
        padding: 5rpx;

        border-bottom-left-radius: 30px;
        border-top-left-radius: 5px;
        padding-left: 20rpx;
        padding-right: 20rpx;
        font-weight: bold;
        margin-right:3rpx;
    }
    .right-tag{
        /*background: #fff8ee;*/
        /*color: #d3a55e;*/
        font-size: 10px;
        padding: 5rpx;
        border-top-right-radius: 30px;
        border-bottom-right-radius: 5px;
        padding-left: 20rpx;
        padding-right: 20rpx;
        font-weight: bold;
        overflow-x: hidden;
        white-space: nowrap;
        text-overflow:ellipsis;
        width: 160rpx;
    }
    .border-3px{
        border:2px solid #c22b14;
    }
    .money-item{
        position: relative;
        overflow: hidden;
    }
    .my-badge{
        /*background: #d84830;*/
        padding-left: 15px;
        padding-right: 10px;
        color: white;
        position: absolute;
        border-bottom-left-radius: 30rpx;
        font-size: 10px;
        top: 0;
        right: 0;
    }
    .money-item-choose{
        background: rgba(250, 209, 24, 0.2);
    }
    .bg-white-milk{
        background-color: rgb(248,246,231);
        color: var(--darkGray);
    }



</style>
