<template>
    <div class="index">
         <div class="message">
            <p class="msg-text">欢迎来到未来商城</p>
        </div>
      
        <div class="big-img-box" @click.stop="clearToken">
             <img  src="src/assets/images/index/first_banner@2x.png" alt="" class="big-img">
        </div>
         <tabbar class="m-tabbar">
            <tabbar-item link="/goods">
                <img slot="icon" src="src/assets/images/index/buy_vip@2x.png">
                <span slot="label">购买VIP</span>
            </tabbar-item>
            <tabbar-item @click.native.stop="getMyCode">
                <img slot="icon" src="src/assets/images/index/activation@2x.png">
                <span slot="label">我的激活码</span>
            </tabbar-item>
            <tabbar-item  link="/app" >
                <img slot="icon" src="src/assets/images/index/app_download@2x.png">
                <span slot="label">APP下载</span>
            </tabbar-item>
            <tabbar-item link="/service">
                <img slot="icon" src="src/assets/images/index/service@2x.png">
                <span slot="label">在线客服</span>
            </tabbar-item>
        </tabbar>
        <div class="img-link">
            <a href="javascript:">
                <img src="src/assets/images/index/t_999@2x.png" alt="" class="big-img">
            </a>
            <div class="flex-box">
                <a href="javascript:">
                    <img src="src/assets/images/index/1@2x.png" alt="">
                </a>
                <a href="javascript:">
                    <img src="src/assets/images/index/2@2x.png" alt="">
                </a>
                <a href="javascript:">
                    <img src="src/assets/images/index/3@2x.png" alt="">
                </a>
                <a href="javascript:">
                    <img src="src/assets/images/index/4@2x.png" alt="">
                </a>
                
            </div>
            <swiper class="swiper" :aspect-ratio="279/709" :auto="true" dots-position="center">
                <swiper-item class="swiper-demo-img" v-for="(item, index) in demo04_list" :key="index">
                    <img :src="item">
                </swiper-item>
            </swiper>
        </div>
    </div>
    
</template>
<script>
    import { Swiper, SwiperItem, Tabbar, TabbarItem } from 'vux'
    import tool from '@/utils/tool'
    import api from '@/api'
    export default {
        data (){
            return{
                demo04_list:[
                    '/src/assets/images/index/bottom_banner1@2x.png',
                    '/src/assets/images/index/bottom_banner2@2x.png',
                    '/src/assets/images/index/bottom_banner3@2x.png'
                ]
            }
        },
        components:{ Swiper, SwiperItem, Tabbar, TabbarItem },
        methods:{
            setMsgAnimation (){
                var _box = document.querySelector('.message');
                var _text = document.querySelector('.msg-text');
                var left = 0;
                // if(_text.clientWidth > _box.clientWidth){
                    setInterval(function(){
                        left += 2;
                        _text.style.left = - left +'px'
                        if(left > ( _text.clientWidth)){
                            left = -10;
                        }
                    },100)
                // }
            },
            getMyCode (){
                let _this = this;
                api.getMyCode().then(data =>{
                    _this.$router.push('/code')
                }).catch(e =>{
                    _this.showTips('您尚未获得激活码')
                })
            },
            clearToken(){
                this.$router.push('/goods')
            }
        },
        created() {
            let _this = this;
            let code = tool.utils.getUrlParam('code');
            let doUid = this.$route.query.uid;
            if(doUid){
                tool.local.set('doUid',doUid);
            }
            if(code){
                if(!this.$store.getters.token){
                    let _doUid = this.$route.query.uid || tool.local.get('doUid') || null;
                    api.login({
                        code:code,
                        upId:_doUid
                    }).then(data =>{
                        if(data.token){
                            _this.$store.commit('SET_TOKEN',data.token);
                            tool.local.set('nickName',data.name);
                            let _location = window.location.origin;
                            // window.location.href = _location;
                            // _this.$router.push(_location);
                            // _this.getHomeDataFun();
                        }
                        if(data.uid){
                            tool.local.set('uid',data.id);
                        }
                    }).catch(e =>{})
                }
                
            }else{
                 if(!this.$store.getters.token){
                        // tool.local.set('isAuth','on');
                    setTimeout(()=>{
                        window.location.href = 'https://open.weixin.qq.com/connect/oauth2/authorize?appid=wxfd808625986ae581&redirect_uri=http%3a%2f%2fwsyc.xiongangs.com%2f%23%2f&response_type=code&scope=snsapi_userinfo&state=STATE#wechat_redirect'
                    },100)
                }
            }
           
        },
        mounted() {
            let _this = this;
            this.$nextTick(()=>{
                _this.setMsgAnimation()
                
            })
        },
    }
</script>
<style lang="less" scoped>
    .index{
        .message{
            width: 50%;
            margin: 5px auto;
            padding: 8px 5px;
            overflow: hidden;
            position: relative;
            height: 25px;
            // border: 1px solid #f3f3f3;
            
            p{
                display: block;
                min-width: 105%;
                text-align: center;
                position: absolute;
                /* width: auto; */
                font-size: 16px;
                white-space: nowrap;
            }
        }
        .m-tabbar{
            position: initial;
            background-color: #fff;
        }
        >.big-img-box{
            padding: 8px 15px;
            img{
            width: 100%;

            }
        }
        .img-link{
            padding: 15px;
            .big-img{
                width: 100%;
            }
            .flex-box{
                // display: flex;
                // flex-wrap: wrap;
                img{
                    display: inline-block;
                    width: 49%;

                    height: auto;
                    margin: 5px auto;
                }
            }
        }
        .m-tabbar{
            padding: 10px 0;
        }
        .swiper{
            img{
                width: 100%
            }
        }
    }
</style>
