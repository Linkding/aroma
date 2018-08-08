<template>
    <div>
        <Nav :pushDown="true"/>
        <div class="container">
            <div class="col-lg-3">
                <SideNav/>
            </div>
            <div class="col-lg-6">
                <h2>常规设置</h2>
                <div>
                    <div class="cute-form">
                        <form v-if="show.username" @submit.prevent="submit('username')">
                            <input type="text" autofocus v-model="current.username">
                            <button class="btn" type="submit">确定</button>
                            <button class="btn" @click="show.username = false" type="button">取消</button>
                        </form>
                        <div v-else class="wrap-row">
                            <div class="col-lg-2">
                                <span class="key">用户名</span>
                            </div>
                            <div class="col-lg-8">
                                <span class="value">{{current.username||'-'}}</span>
                            </div>
                            <div class="col-lg-2">
                                <button @click="show.username = true" class="btn">编辑</button>
                            </div>
                        </div>
                    </div>
                     <div class="cute-form">
                        <form v-if="show.email" @submit.prevent="submit(email)">
                            <input type="text" autofocus v-model="current.email">
                            <button class="btn" type="submit">确定</button>
                            <button class="btn" @click="show.email = false" type="button">取消</button>
                        </form>
                        <div v-else class="wrap-row">
                            <div class="col-lg-2">
                                <span class="key">邮箱</span>
                            </div>
                            <div class="col-lg-8">
                                <span class="value">{{current.email||'-'}}</span>
                            </div>
                            <div class="col-lg-2">
                                <button @click="show.email = true" class="btn">编辑</button>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>
<script>
    import Nav from '../components/Nav';
    import SideNav from '../components/SideNav';
    import '../css/sidenav.css';
    import session from '../lib/session';
    import api from '../lib/api';

    export default {
        components:{Nav , SideNav},
        data(){
            return{
                current:session.uinfo(),
                show:{
                    username:false,
                    email:false,
                }
            }
        },
        methods:{
            submit(property){
                api('user/update',this.current)
                    .then(r=>{
                        session.replace_uinfo(r.data);
                        this.show[property] = false;
                    })
            }
        }
    }
</script>
<style scoped>
.cute-form{
    padding: 7px;
    border-bottom: 1px solid rgba(0, 0, 0, .1)
}
.cute-form form >* {
    margin: 5px;
}

.key {
    font-size: 1.3rem;
    color: #969696;
}
.value {
    font-size: 1rem;
}
</style>
