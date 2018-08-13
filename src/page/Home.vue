<template>
    <div>
        <div class="header">
            <Nav :is_poa="true" :dropTop="'99.5%'" :dropUserRight="'4.5%'" :dropCartRight="'0%'"/>
            <!-- <div class="slider" v-for="(pre,index) in cat" :key="index">
                <img :src="pre.cover_url" alt="">
            </div> -->
            <div v-if="cat.length">
                <swiper :options="swiperOption" class="slider">
                    <swiper-slide v-for="(it, index) in cat" :key="index">
                        <!-- <div class="bg-img" :style="{'background-image': 'url(' + it.cover_url+ ')'}"></div> -->
                        <img :src="it.cover_url" style="width:100%">
                    </swiper-slide>
                    <div class="swiper-pagination" slot="pagination"></div>
                </swiper>
            </div>
        </div>
        <div class="cat">
            <div class="container">
                <div class="cat-list">
                    <div class="cat-title">
                        明星单品
                        <span class="des"> GLOBAL BUYER</span>
                    </div>
                    <div class="row card-group">
                         <div class="card" v-for="(card,index) in start_hot" :key="index">
                             <router-link :to="'/detail/' + card.id">
                                <img :src="card.preview && card.preview[0] &&card.preview[0].url" alt="">
                             </router-link>
                            <span class="tap start-hot">{{start_hot_tap[index]}}</span>
                        </div>
                    </div>
                </div>
                <div class="cat-list">
                    <div class="cat-title">
                        法国推荐餐酒
                        <span class="des">FRANCE DINNER WINE</span>
                    </div>
                    <div class="row card-group">
                        <div class="card" v-for="(card,index) in occasion_hot" :key="index">
                             <router-link :to="'/detail/' + card.id">
                                <img :src="card.preview && card.preview[0] &&card.preview[0].url" alt="">
                            </router-link>
                            <span class="tap occasion-hot">家庭餐</span>
                        </div>
                    </div>
                </div>
                <div class="cat-list">
                    <div class="cat-title">
                        热卖系列
                        <span class="des">HOT SELLER</span>
                    </div>
                    <div class="row card-group">
                        <div class="card single-hot" v-for="(card,index) in single_hot" :key="index">
                            <router-link :to="'/detail/' + card.id">
                                <img :src="card.preview && card.preview[0] &&card.preview[0].url" alt="">
                            </router-link>
                            <span class="tap">{{single_hot_tap[index]}}</span>
                        </div>
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
    import Nav from '../components/Nav';
    import Footer from '../components/Footer';
    import api from '../lib/api';

    export default {
        components:{Nav , Footer},
        data(){
            return{
                cat:{},
                start_hot:{},
                start_hot_tap:['酒具','博若莱','香 槟','桃红酒'],
                single_hot:{},
                single_hot_tap:['@瑞 士','@新西兰','@智 利','@澳大利亚'],
                occasion_hot:{},
                occasion_hot_tap:[],
                swiperOption : {
                    keyboard   : true,
                    clickable  : true,
                    loop       : true,
                    autoplay   : {
                        deplay:500,
                    },
                    pagination : {
                        el : '.swiper-pagination',
                    },
                },
                swiperSlides : [ 1, 2, 3 ],
                }
        },
        mounted() {
            this.read();
            this.read_by_segment('start_hot');
            this.read_by_segment('single_hot');
            this.read_by_segment('occasion_hot');
        },
        methods:{
            read(){
                api('category/read')
                    .then(r=>{
                        this.cat = r.data;
                    })
            },
            read_by_segment(segment){
                api('product/search',{
                    or:{
                        [segment]:true,
                    }
                }).then(r=>{
                    this[segment] = r.data; 
                })
            }
        }
    }
</script>
<style scoped>
.header {
    position: relative;
}
/* .bg-img{
   background-repeat: no-repeat;
   background-position: contain;
   height: 500px; 
} */
.slider {
    opacity: .85;
}
.cat {
    margin: 2rem 0;
}
.cat .cat-title{
    font-size: 2.5rem;
    font-weight: 700;
    padding: 0px 12px;
    margin: 3rem 0;
    border-left: 3px solid rgba(0, 0, 0, 1)
}
.cat .cat-title .des{
    font-size: 1.2rem;
    font-weight: normal;
    color: #969696;
}
.cat .card {
    position: relative;
    padding: 0;
    margin: 0;
}
.cat .card-group  {
    -moz-column-count: 4;
    -webkit-column-count: 4;
    column-count: 4;
    -moz-column-gap:15px;
    -webkit-column-gap:15px;
    column-gap:15px;
}
.cat .card img{
    height: 350px;
}
.cat .card .tap{
    font-size: 1.5rem;
    position: absolute;
    bottom: 10px;
    left: 5px;
    background: #F9726C;
    color: #fff;
    padding: 3px 15px;
    opacity: .8;
}
.cat .card .tap.start-hot{
    background: #463931;
}
.cat .card .tap.occasion-hot{
    background: #463931;
}
.cat .card.single-hot img{
    height: 300px;
}
.cat .card.single-hot .tap{
    font-size: 1.5rem;
    opacity: .9;
}
</style>
