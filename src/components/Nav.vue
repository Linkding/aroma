<template>
    <div :style="{marginBottom: this.pushDown ? '20px': 0,}" :class="['container' , 'nav' , is_poa?'poa':'']">
        <div class="col-lg-4 ">
            <router-link to="/">
                <img class="logo" src="../assets/img/logo-v2.png" alt="">
            </router-link>
        </div>
        <div class="col-lg-6 right">
            <SearchBar/>
        </div>
        <div class="col-lg-2 right">
            <div v-if="uinfo">
                <div class="user-panel" @mouseover="show_user_drop =true"  @mouseleave="show_user_drop = false">
                    <div>
                        <div class="nav-item username" >{{uinfo.username}}</div>
                    </div>
                    <transition name="tr-user">
                        <div class="user-drop" v-if="show_user_drop" :style="{top:dropTop,right:dropUserRight}">
                            <div v-if="uinfo.username == 'linkding'">
                                <router-link to="/admin/user">
                                    <i class="fa fa-tasks" aria-hidden="true"></i>
                                    管理后台
                                </router-link>
                            </div>
                            <div>
                                <router-link to="/order/meorder">
                                    <i class="fa fa-bars" aria-hidden="true"></i> 
                                    我的订单
                                </router-link>
                            </div>
                            <div>
                                <router-link to="/me/setting">
                                    <i class="fa fa-cog" aria-hidden="true"></i>
                                    设置
                                </router-link>
                            </div>
                            <div>
                                <div @click="logout">
                                    <i class="fas fa-sign-out-alt"></i>
                                    退出
                                </div>
                            </div>
                        </div>
                    </transition>
                </div>
                <div class="cart-panel" @mouseover="onShowCart" @mouseleave="show_cart_drop=false">
                    <!-- <div > -->
                        <div class="nav-item cart">
                            <!-- 购物车 -->
                            <i class="fas fa-cart-arrow-down"></i>    
                        </div>
                    <!-- </div> -->
                    <transition name="tr-cart">
                        <div class="cart-drop" v-if="show_cart_drop" :style="{top:dropTop,right:dropCartRight}">
                            <div class="cart-drop-header">
                                <div v-if="cart">
                                    <input type="checkbox" id="allselect" @click="toggle_check" v-model="check_all">
                                    <label for="allselect">全选</label>
                                </div>
                                <div v-else class="empty">
                                    空空如也~
                                </div>
                            </div>
                            <div class="cart-warp">
                                <!-- <transition name="tr-cartlist"> -->
                                    <div  class="cart-list" v-for="(item,index) in cart" :key="index">
                                        <div class="col-lg-1">
                                            <input type="checkbox" v-model="item._checked">
                                        </div>
                                        <div class="col-lg-2">
                                            <img :src="item.$product.preview[2].url" alt="">
                                        </div>
                                        <div class="col-lg-8 info">
                                            <div class="name">{{item.$product.name}}</div>
                                            <div>
                                                <div class="col-lg-6 count">x {{item.count}}</div>
                                                <div class="col-lg-6">
                                                    <span class="currency">{{item.$product.price}}</span>
                                                </div>
                                            </div>
                                        </div>
                                        <div class="col-lg-1">
                                            <div class="remove" @click="remove(item.id)">
                                                <i class="fa fa-times" aria-hidden="true"></i>
                                            </div>
                                        </div>
                                    </div>
                                <!-- </transition> -->
                            </div>
                            <div class="sub-bar" v-if="cart">
                                <div class="col-lg-8">
                                    <!-- <div>已选：{{selected}}</div> -->
                                    <span>商品共计： </span><span class="currency">{{total}}</span>
                                </div>
                                <div class="col-lg-4">
                                    <router-link :to="to()">
                                        <button class="sub-btn">
                                            提交订单
                                        </button>
                                    </router-link>
                                </div>
                            </div>
                            
                        </div>
                    </transition>
                </div>
            </div>
            <div v-else>
                <router-link  class="nav-item" to="/login">登录</router-link>
                <router-link class="nav-item" to="/signup">注册</router-link>
            </div>
        </div>
    </div>
</template>
<script>
    import session from '../lib/session';
    import SearchBar from './SearchBar';
    import api from '../lib/api';

    export default {
        components:{SearchBar},
        props:{
            is_poa:{
                default:false,
            },
            pushDown: {
                default: false
            },
            dropTop:'',
            dropUserRight:'',
            dropCartRight:''
        },
        data(){
            return{
                uinfo:session.uinfo(),
                show_user_drop:false, //用户下拉面板
                show_cart_drop:false, //购物车下拉面板
                cart:{},
                check_all:false, //全选状态
                with:[
                        {model:'product',relation:'has_one'}
                    ],
            }
        },
        computed:{
            total(){
                let sum = 0
                let cart = this.cart;
                for(let key in cart){
                    let val = cart[key];
                    if(!val._checked)
                        continue;
                    sum += val.$product.price * val.count
                }
                return sum;
            },
            selected(){
                let sum = 0;
                let cart = this.cart;
                for(let key in cart){
                    let val = cart[key];
                    if(val._checked)
                        sum += 1
                }
                return sum;
            }
        },
        mounted() {
            this.read();
        },
        methods:{
            onShowCart(){
                this.show_cart_drop = true;
                this.check_all = false;
            },
            logout(){
                session.logout();
            },
            read(){
                api('cart/read',{
                    where:{
                        user_id:this.uinfo.id,
                    },
                    with:this.with
                }).then(r=>{
                        this.cart = r.data;
                    })
            },
            toggle_check(){
                let cart = this.cart;
                for(let key in cart){
                    let val = cart[key];
                    this.$set(val,'_checked',!this.check_all)
                }
            },
            remove(id){
                api('cart/delete',{id})
                    .then(r=>{
                        this.read();
                    })
            },
            to(){
                let check = [];
                let cart = this.cart;
                for(let key in cart){
                    let val = cart[key];
                    if(val._checked)
                        check.push(val.id)
                }
                return {path:'/order/confirm', query:{id:check,_referer:'cart_buy'}}
            }
        }
    }
</script>
<style scoped>
.poa{
    z-index: 10;
    position: absolute;
}
.fa-times{
    font-size: 1.2rem;
}
.fa-bars,
.fa-cog{
    margin-right: 5px;
}
.currency{
    color: #F9726C;
}
.nav{
    background: #F1EDEE;
    top:5%;
    left: 10%;
    right: 10%;
    width: 100%;
    border-radius: 5px;
    padding: 3px 0;
}

.nav >*{
    /* font-size: 0.8rem; */
    vertical-align: middle;
}
.count-btn {
    background: rgba(0, 0, 0, .09);
}
.nav .logo {
    padding-left: 30px;
    width: 150px;
    height: 55px;
}
.nav .item {
    text-align: center;
}
.nav a {
    color: rgba(0, 0, 0, .8);
    font-size: 1rem;
}
.nav-item {
  display: inline-block;
  padding: 1.5rem;
  color: #555;
  text-decoration: none;
}
.user-panel,
.cart-panel {
 display: inline-block;
}
.user-panel .username,
.user-panel .cart{
    position: relative;
    cursor:pointer
}
.user-drop,
.cart-drop {
    position: absolute;
    background: #fff;
    top:9.1%;
    border: 1px solid rgba(0, 0, 0, .08);
    box-shadow: 1px 1px 2px rgba(0, 0, 0, .08);
    z-index: 20;
    border-radius: 3px;
}
.cart-drop .cart-warp {
    height: 300px;
    overflow: scroll;
}
.user-drop{
    width: 110px;
    right: 13.9%;
    padding: 8px 10px;
}
.cart-drop {
    width: 350px;
    right: 10%;
    text-align: left;
    border: 1px solid rgba(0, 0, 0, .0)
}
.cart-drop .empty{
    padding: 10px;
    text-align: center;
}

.cart-drop-header{
    /* background: #F1EDEE; */
    min-height: 25px;
    padding: 8px;
    border-bottom: 1px solid rgba(0, 0, 0, .08);
}
.cart-drop-header label {
    padding: 10px;
}
.cart-drop .cart-list {
    padding: 7px 0;
    /* border-bottom: 1px solid rgba(0, 0, 0, .08); */
    margin-bottom: 7px;
    /* background: #F1EDEE; */
}
.cart-drop .cart-list > *{
    font-size: 0.8rem;
}
.cart-drop .cart-list .info {
    padding: 0 10px;
}
.cart-drop .cart-list .info >*{
    padding: 6px;
}
.cart-drop .cart-list img{
    padding-left: 10px;
    width: 80px;
    height: 60px;
}
.cart-drop .cart-list .count{
    font-size: 0.8rem;
    color: #999;
}
.cart-drop .cart-list .count-bar,
.cart-drop .cart-list .remove {
    text-align: center;
}
.cart-drop .cart-list .remove >* {
    color: #999;
}
.cart-drop .cart-list .remove {
    margin-top: 10px;
}
.cart-drop  .sub-bar {
    background: #F1EDEE;
    margin: 0;
    padding: 10px;
}
.cart-drop  .sub-btn{
    background: #F9726C;
    border-radius: 3px;
    padding: 8px;
    color: #fff;
    text-align: center;
}
.cart-drop  .sub-first {
    padding: 8px;
}
.user-drop > *{
    font-size: 1rem;
    padding: 8px 5px;
    text-align: left;
}
.tr-user-enter-active, .tr-user-leave-active {
  transition: opacity .9s
}
.tr-user-enter, .tr-user-leave-to /* .fade-leave-active in below version 2.1.8 */ {
  opacity: 0
}
.tr-cart-enter-active, .tr-cart-leave-active {
  transition: opacity .9s
}
.tr-cart-enter, .tr-cart-leave-to /* .fade-leave-active in below version 2.1.8 */ {
  opacity: 0
}
.tr-cartlist-enter-active, .tr-cartlist-leave-active {
  transition: opacity .9s
}
.tr-cartlist-enter, .tr-cartlist-leave-to /* .fade-leave-active in below version 2.1.8 */ {
  opacity: 0
}
</style>
