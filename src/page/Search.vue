<template>
    <div>
        <div class="header">
            <Nav/>
        </div>
        <div class="container">
            <div @mouseleave="cat_panel = ''" class="search-area">
                <div  class="search-bar">
                    <span @mouseover="cat_panel = 'wine'" class="search-item">
                        <img src="../assets/bottle02-icon.png" alt="">
                        <div>餐酒</div>
                    </span>
                    <span @mouseover="cat_panel = 'winetool'"  class="search-item">
                        <img src="../assets/glass-icon.png" alt="">
                        <div>酒具</div>
                    </span>
                    <span  @mouseover="cat_panel = 'wineparty'" class="search-item">
                        <img src="../assets/wineparty02-icon.png" alt="">
                        <div>宴会</div>
                    </span>
                    <span @mouseover="cat_panel = 'new'" class="search-item">
                        <img src="../assets/newwine-icon.png" alt="">
                        <div>新酒</div>
                    </span>
                </div>
                <div class="cat-panel">
                    <div  v-show="cat_panel == 'wine'" class="wine">
                        <div class="panel">
                            <div class="col-lg-1 title">品种</div> 
                            <div class="col-lg-11 link-group" >
                                <span v-for="(item,index) in breed_list" :key="index">
                                    <span @click="set_query_where('breed_id',item.id)">{{item.name}}</span>
                                </span>
                                <!-- <span>梅洛</span>
                                <span>西拉</span>
                                <span>霞多丽</span>
                                <span>长相思</span> -->
                            </div>
                        </div>
                        <div class="panel">
                        <div class="col-lg-1 title">地区</div> 
                        <div class="col-lg-11 link-group">
                            <span v-for="(item,index) in location_list" :key="index">
                                <span @click="set_query_where('location_id',item.id)">
                                    {{item.name}}
                                </span>
                            </span>
                            <!-- <span>西班牙</span>
                            <span>意大利</span>
                            <span>澳大利亚</span>
                            <span>智利</span> -->
                        </div>
                        </div>
                        <div class="panel">
                        <div class="col-lg-1 title">年份</div> 
                        <div class="col-lg-11 link-group">
                            <span @click="set_query_year_range(2015,2017)">2015-2017</span>
                            <span>2011-2014</span>
                            <span>2008-2010</span>
                            <span>&lt; 2008</span>
                        </div>
                        </div>
                    </div>
                    <div v-show="cat_panel == 'winetool'" class="winetool">
                        <div class="panel">
                            <div class="col-lg-1 title">类型</div> 
                            <div class="col-lg-11 link-group">
                                <span v-for="(item,index) in ptype_list" :key="index">
                                    <span @click="set_query_where('ptype_id',item.id)">
                                        {{item.name}}
                                    </span>
                                </span>
                                <!-- <span>醒酒器</span>
                                <span>开瓶器</span>
                                <span>酒架</span>
                                <span>瓶嘴</span> -->
                            </div>
                        </div>
                        <div class="panel">
                            <div class="col-lg-1 title">材质</div> 
                            <div class="col-lg-11 link-group">
                                <span v-for="(item,index) in material_list" :key="index">
                                    <span  @click="set_query_where('material_id',item.id)">
                                        {{item.name}}
                                    </span>
                                </span>
                            </div>
                        </div>
                        <div class="panel">
                            <div class="col-lg-1 title">型号</div> 
                            <div class="col-lg-11 link-group">
                                <span>波尔多红酒杯</span>
                                <span>勃艮第红酒杯</span>
                                <span>香槟杯</span>
                                <span>甜酒杯</span>
                                <span>长相思杯</span>
                            </div>
                        </div>
                    </div>
                    <div v-show="cat_panel == 'wineparty'" class="wineparty">
                        <div class="panel">
                            <div class="col-lg-1 title">场合</div> 
                            <div class="col-lg-11 link-group">
                                <span v-for="(item,index) in occasion_list" :key="index">
                                    <span @click="set_query_where('occasion_id',item.id)">
                                        {{item.name}}
                                    </span>
                                </span>
                                <!-- <span>朋友</span>
                                <span>商务</span>
                                <span>郊游</span>
                                <span>婚礼</span> -->
                            </div>
                        </div>
                    </div>
                    <div v-show="cat_panel == 'new'" class="winenew">
                        <div class="panel">
                            <div class="col-lg-1 title">新酒</div> 
                            <div class="col-lg-11 link-group">
                                <span>博若莱新酒</span>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
        <div class="card-group" v-if="list">
            <div
                v-for="(pre,index) in list"
                :key="index"
                class="box"
            >
                <div class="pic">
                    <router-link :to="'/detail/'+pre.id">
                        <img :src='pre.preview[0].url' alt="">
                    </router-link>
                    <div class="info heart">
                        <span>
                            <i class="fa fa-heart" aria-hidden="true"></i>
                        </span>
                    </div>
                    <div class="info plus">
                        <!-- <span><i class="fa fa-plus" aria-hidden="true"></i></span> -->
                        <router-link :to="'/detail/' + pre.id">
                            <!-- <span>view</span> -->
                            <i class="fa fa-asterisk" aria-hidden="true"></i>
                        </router-link>
                    </div>
                </div>
            </div>
        </div>
        <div v-else class="empty-area">
            <div class="empty-search"></div>
            <div class="em-desc">暂无内容</div>
        </div>
        <Pagination 
            :totalCount="total"
            :limit="limit"
            :onChange="on_page_change"
        />
        <div class="container">
            <Footer/>
        </div>
    </div>
</template>
<script>
import Nav from "../components/Nav";
import Footer from "../components/Footer";
import Pagination from "../components/Pagination";
import api from '../lib/api';
import {clone} from '../lib/helper';

export default {
    components: { Nav , Pagination,Footer},
    data(){
        return{
            cat_panel:'',
            list:{},
            total:0,
            limit:10,
            search_param:{},
            breed_list:[],
            location_list:[],
            ptype_list:[],
            occasion_list:[],
            material_list:[],
        }
    },
    mounted() {
        this.read();
        this.read_by_model('breed');
        this.read_by_model('location');
        this.read_by_model('ptype');
        this.read_by_model('occasion');
        this.read_by_model('material');
    },
    methods:{
            read(){
                api('product/read',{limit:this.limit})
                    .then(r=>{
                        this.list = r.data;
                        this.total = r.total;
                    })
            },
            read_by_model(model){
                api(`${model}/read`)
                    .then(r=>{
                        this[model + '_list']= r.data;
                    })
            },
            on_page_change(page){
                this.set_condition('page',page);
            },
            //set_condition为请求的api中的专有属性
            set_condition(type,value){ 
                let query = clone(this.$route.query);
                switch (type){
                    case 'page':
                        query.page = value;
                        break;
                }
                this.$router.replace({query});
                this.search();
            },
            //set_query_where为请求中对数据范围的属性值
            set_query_where(type,value){
                let condition = {};
                condition[type] = value;

                // let o = this.search_param;
                // let n = Object.assign({},o,condition);
                this.$router.replace({query:condition});
            },
            set_query_year_range(min,max){
                let query = this.parse_route_query();
                if(!min && !max){
                    delete query.year_min;
                    delete query.year_max;
                }else{
                    let condition = {
                        year_min :min,
                        year_max :max,
                    };
                    query = Object.assign({},query,condition);
                }
                this.$router.replace({query});
            },
            search(){
                let p = this.search_param;
                
                let breed_query = ''
                    ,location_query = ''
                    ,ptype_query = ''
                    ,occasion_query = ''
                    ,material_query = ''
                    ,year_min_query = ''
                    ,year_max_query = ''
                    ;
                p.breed_id &&( breed_query = `and "breed_id" = ${p.breed_id}`);
                p.location_id &&( location_query = `and "location_id" = ${p.location_id}`);
                p.occasion_id &&( occasion_query = `and "occasion_id" = ${p.occasion_id}`);
                p.material_id &&( material_query = `and "material_id" = ${p.material_id}`);
                p.year_max && ( year_max_query = `and "year" <= ${p.year_max}`);
                p.year_min && ( year_min_query = `and "year" >= ${p.year_min}`);

                let query = `where("name" contains "${p.keyword||''}" ${breed_query} ${location_query} ${occasion_query} ${material_query} ${year_min_query} ${year_max_query})`

                api('product/read',{
                    query:query,
                    limit:this.limit,
                    page:p.page,
                }).then(r=>{
                    this.list = r.data;
                    this.total = r.total;
                })
            },
            prepare_search_param(){
                let query = this.parse_route_query();
                this.search_param = query;
                
            },
            parse_route_query(){
                let query = this.$route.query;
                
                if(!query.sort_by)
                    query.sort_by = ['id','down'];
                
                if(typeof query.sort_by == 'string')
                    query.sort_by = query.sort_by.split(',')
                return query;
            },
            // clean_query(){
            //     this.$router.replace({});
            // }

    },
    watch:{
        '$route.query':{
            deep:true,
            handler(){
                this.prepare_search_param();
                this.search();
            }
        }
    }
};
</script>
<style scoped>
.fa-asterisk{
    color: #77D1E2;
}
.search-area {
    position: relative;
}
.search-bar {
  background: rgb(224, 224, 224);
  padding: 10px;
  text-align: center;
}
.search-item {
  padding: 10px;
  margin: 0 20px;
}
.search-item img {
  width: 30px;
  height: 30px;
  opacity: 0.7;
}
.search-item:hover {
  background: #ccc;
}
.cat-panel {
    position: absolute;
    top:100%;
    left: 0%;
    z-index: 1;
    width: 100%;
    border: 1px solid rgba(0, 0, 0, 0.07);
    opacity: 1;
}
.cat-panel .panel {
  background: #f8f8f6;
  color: rgba(0, 0, 0, 0.7);
  cursor: pointer;
}
.cat-panel .panel .title {
    font-size: 1.1rem;
    text-align: center;
}
.cat-panel .panel .title,
.cat-panel .panel .link-group > * {
  padding: 10px 20px;
}
.card-group {
    -moz-column-count: 3;
    -webkit-column-count: 3;
    column-count: 3;
    /* -webkit-column-width: 250px;
    -moz-column-width: 250px;
    column-width: 250px; */
    -moz-column-gap:20px;
    -webkit-column-gap:20px;
    column-gap:20px;
    padding:30px 10%;
}
.box{
    box-sizing: border-box;    
    padding: 5px;
    /* border: solid 2px #eeeeee; */
    border-radius: 4px;
    margin-bottom: 30px;
    cursor: pointer;
}
.pic {
    position: relative;
}
.pic img{
    width: 100%;
    opacity: .8;
}
.box .info {
    /* visibility: hidden; */
    /* display: none; */
    opacity: 0;
    font-size: 1rem;
    position: absolute;
    color: #F9726C;
    padding: 8px;
    border-radius: 5px;
    background: #fff;
    right: 10%;
}
.box .heart{
    bottom:18%;
    
}
.box .plus{
    bottom:5%;
}

.box:hover .info {
    opacity: .8;
    /* visibility: visible; */
    /* display: inline-block; */
    opacity: 1;
}
.box .pic:hover img{
    opacity: 1;
}
.empty-area {
    width: 100%;
    height: 480px;
    text-align: center;
    padding-top: 200px;
}
.empty-search{
    background-image: url(../assets/empty-search.png);
    background-repeat: no-repeat;
    background-position: center;
    background-size: contain;
    height: 80px;
}
.em-desc {
    padding: 15px;
    color: #ccc;
}
</style>
