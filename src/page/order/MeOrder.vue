<template>
    <div>
        <div class="mask" v-if="show_detail_mask">
            <div class="wrap-detail">
                <div class="close right" @click="show_detail_mask = false">
                </div>
                <div>
                    <table>
                        <thead>
                            <th>序号</th>
                            <th>商品名称</th>
                            <th>单价</th>
                            <th>数量</th>
                            <th>小计</th>
                        </thead>
                        <tbody>
                            <tr v-for="(item,index) in product_info" :key="index">
                                <td>{{item.$product?item.$product.id:item.id}}</td>
                                <td>{{item.$product?item.$product.name:item.name}}</td>
                                <td>{{item.$product?item.$product.price:item.price}}</td>
                                <td>{{item.count||'-'}}</td>
                                <td>{{item.$product?item.$product.price:item.price * item.count }}</td>
                            </tr>
                        </tbody>
                    </table>
                </div>
            </div>
        </div>
        <Nav :pushDown="true"/>
        <div class="container">
            <div class="col-lg-3">
                <SideNav/>
            </div>
            <div class="col-lg-9">
                <div class="header">
                    <div :class="'col-lg-2 '+ 'item '  + (onselect == 'i1'?'active':'')" @click="onselect = 'i1';read()">全部订单</div>
                    <div :class="'col-lg-2 '+ 'item '+ (onselect == 'i2'?'active':'')" @click="onselect = 'i2';read('_paid',false)">待支付</div>
                    <div :class="'col-lg-2 '+ 'item '+ (onselect == 'i3'?'active':'')" @click="onselect = 'i3';read('wait_delivery',true)">已发货</div>
                    <div :class="'col-lg-2 '+ 'item '+ (onselect == 'i4'?'active':'')" @click="onselect = 'i4';read('wait_delivery',false)">待发货</div>
                    <div :class="'col-lg-2 '+ 'item '+ (onselect == 'i5'?'active':'')" @click="onselect = 'i5'">待评价</div>
                </div>
                <div class="table">
                    <table v-if="list">
                        <thead v-if="onselect == 'i1' ||onselect == 'i2'" >
                            <th v-for="(head,index) in thead.i1" :key="index">{{head}}</th>
                        </thead>
                        <thead v-if="onselect == 'i3'||onselect == 'i4'">
                            <th v-for="(head,index) in thead.i2" :key="index">{{head}}</th>
                        </thead>
                        <thead>

                        </thead>
                        <tbody>
                            <tr v-for="(row,index) in list" :key="index">
                                <td>{{row.oid||'-'}}</td>
                                <td>{{row.sum||'-'}}</td>
                                <td><span @click="show_detail(row.product_info)" class="check">查看</span></td>
                                <td>{{row.pay_by||'-'}}</td>
                                <td>
                                    <div v-if="row._paid">
                                        {{row._paid?'是':'否'}}
                                    </div>
                                    <div v-if="row.wait_delivery">{{row.wait_delivery?'是':'否'}}</div>
                                </td>
                                <td>{{row.memo||'-'}}</td>
                                <td>
                                    <div v-if="!row._paid">
                                        <router-link :to="`/pay/${row.oid}`" class="btn">付款</router-link>
                                    </div>
                                    <div v-else>已支付</div>
                                </td>
                                <td>
                                    <span @click="cancel(row.id)" class="btn">删除订单</span>
                                    <!-- <div v-if="!row._paid">
                                        <router-link :to="`/pay/${row.oid}`" class="btn">付款</router-link>
                                    </div>
                                    <div v-else>订单完成</div> -->
                                </td>
                            </tr>
                        </tbody>
                    </table>
                    <div v-else class="col-lg-12 empty-area">
                        <div class="empty-img"></div>
                        <!-- <img src="../../assets/empty-shopcar.png" alt=""> -->
                        <p class="em-desc">暂无内容</p>
                    </div>
                </div>
                
            </div>
        </div>
        <div class="container">
            <Footer/>
        </div>
    </div>
</template>
<script>
    import session from '../../lib/session';
    import api  from '../../lib/api';
    import Nav from '../../components/Nav';
    import Footer from '../../components/Footer';
    import SideNav from '../../components/SideNav';

    export default{
        components:{Nav,SideNav,Footer},
        data(){
            return{
                thead:{
                    i1:['订单号','总价','订单信息','付款方式','已付款','备注','订单状态','操作'],
                    i2:['订单号','总价','订单信息','付款方式','发货状态','备注','订单状态','操作'],
                },
                show_detail_mask:false,
                product_info:{},
                uinfo:session.uinfo(),
                list:{},
                onselect:'i1',
                with:[
                    {model:'user',relation:'has_one'},
                ]
            }
        },
        mounted() {
            this.read();
        },
        methods:{
            show_detail(row){
                this.product_info = [row];
                this.show_detail_mask = true;
            },
            read(key,value){
                this.list = {};
                api('order/search',{
                    where:{
                        user_id:this.uinfo.id,
                        [key]:value,
                    },
                    with:this.with,
                }).then(r=>{
                    this.list = r.data;
                })
            },
            cancel(id){
                api('order/delete',{id})
                    .then(r=>{
                        this.read();
                    })
            }
        },
    }
</script>
<style scoped>
.btn{
    padding: 5px;
    border: 1px solid rgba(0, 0, 0, .1);
    color: #000;
    cursor: pointer;
    margin: 0 2px;
}
.mask {
    position: fixed;
    width: 100%;
    height: 100%;
    background: rgba(0, 0, 0, .1);
}
.wrap-detail {
    position: absolute;
    top: 15%;
    left: 22%;
    width: 700px;
    height: 250px;
    background: #f8f8f6;
    padding: 20px;
}
.check {
   cursor:pointer;
}
.header {
    margin-bottom: 15px;
}
.header .item{
    font-size: 1.3rem;
    border-bottom: 2px solid rgba(0, 0, 0, .1);
    padding: 13px 1px;
    text-align: center;
}
.header .item:hover{
    color: #F9726C;
}
.header .item.active{
    border-color: #F9726C; 
    color: #F9726C   
}
.empty-area {
    width: 620px;
    height: 480px;
    text-align: center;
}
    
.em-desc {
    color: #ccc;
}
.empty-img {
    background-image: url(../../assets/empty-shopcar.png);
    background-repeat: no-repeat;
    background-position: center;
    height: 100px;
}
</style>
