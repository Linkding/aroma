<template>
    <div>
        <Nav />
        <div class="container">
            <div class="product">
                <div class="col-lg-6 pic">
                    <img :src="detail.preview && detail.preview[0] && detail.preview[0].url ? detail.preview[0].url : ''">
                </div>
                <div class="col-lg-6 main">
                    <h1 class="title">{{detail.name}}</h1>
                    <div class="info-item">
                        <span class="key">产地：</span>
                        <span class="val">{{detail.$location? detail.$location.name : '-'}}</span>
                    </div>
                    <div class="info-item">
                        <span class="key">年份：</span>
                        <span class="val">{{detail.year}}</span>
                    </div>
                    <div class="info-item">
                        <span class="key">品种：</span>
                        <span class="val">{{detail.$breed&&detail.$breed.name||'-'}}</span>
                    </div>
                    <div class="info-item">
                        <span class="key">价格：</span>
                        <span class="val currency">{{detail.price}}</span>
                    </div>
                    <div class="info-item">
                        <span class="key">数量</span>
                        <span class="val">
                            <button  type="button" @click="reduce" class="btn btn-primary">-</button>
                            <span class="count">{{count}} </span>
                            <button type="button" @click="add" class="btn btn-primary">+</button>
                        </span>
                    </div>
                    <div class="info-item">
                        <button @click="addCart" type="submit">加入购物车</button>
                        <router-link :to="to()"><button>立即购买</button></router-link>
                    </div>
                </div>
            </div>
            <Footer/>
        </div>
    </div>
</template>
<script>
    import Nav from '../components/Nav'
    import Footer from '../components/Footer'
    import api from '../lib/api';
    import session from '../lib/session';

    export default{
        components:{Nav , Footer},
        data(){
            return{
                detail:{},
                with:[
                    {model:'location',relation:'has_one'},
                    {model:'breed',relation:'has_one'},
                ],
                count:1,
                uinfo:session.uinfo(),
                is_exist_product:{},
            }
        },
        mounted() {
            let id = this.get_id();
            this.find(id);
            this.is_exist();
            
        },
        methods:{
            is_exist(){
                return api('cart/first',{
                    where:{
                        product_id:this.$route.params.id,
                    }
                    }).then(r=>{
                        if(r.data){
                            this.is_exist_product = r.data;
                        }
                    })
            },
            addCart(){
                if(!session.is_login())
                    this.$router.push('/login');

                this.is_exist_product.count = '';
                this.is_exist()
                    .then(r=>{
                        let action = this.is_exist_product.count ? 'update':'create';
        
                        this.is_exist_product.product_id = this.detail.id;
                        this.is_exist_product.user_id = this.uinfo.id;
                        if(action == 'update') {
                            this.is_exist_product.count = this.count + this.is_exist_product.count;
                        }
                        else 
                            this.is_exist_product.count  = this.count
                        // else {
                        //     console.log('333',333);
                        //     this.is_exist_product.count = this.count + 0
                        // }
                        // this.is_exist_product.count = action == 'create'? this.count :this.count + this.is_exist_product ? this.is_exist_product.count:0;
                        
                        api(`cart/${action}`,this.is_exist_product)
                    })

                    
            },
            to(){
                if(!session.is_login())
                    this.$router.push('/login');
                return {path:'/order/confirm', query:{id:this.detail.id,count:this.count,_referer:'item_buy'}}
            },
            reduce(){
                if(this.count <= 0)
                    return this.count = 0;
                return this.count--;
            },
            add(){
                if(this.count >= this.detail.store){
                    return this.count = this.detail.store;
                }
                return this.count++;
            },
            get_id(){
               return this.$route.params.id;
            },
            find(id){
                api('product/find',{
                    id:id,
                    with:this.with
                    }).then(r=>{
                        this.detail = r.data;
                        console.log('this.detail',this.detail);
                        
                    })
            }
        }
    }
</script>
<style scoped>
.product {
    padding: 20px 0;
    background: rgba(0, 0, 0, .03);
}
.main{
    padding-left: 10%;
    height: 440px;
}
.main .info-item{
    padding: 10px 0;
    vertical-align: middle;
}
.info-item button {
    margin: 0 5px;
    border-radius: 4px;
}
.info-item button:hover {
    background: #F9726C;
    color: #fff;
    cursor: pointer;
}
.product .pic {
    padding-left: 10%;
}
.key {
    margin-right:  12px;
}
.count{
    border: 1px solid rgba(0, 0, 0, .05);
    padding: 5px 30px;
    
}
</style>
