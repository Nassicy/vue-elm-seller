<template>
    <header id="header" class="">
        <div class="content-wrapper">
            <div class="avatar">
                <img  v-bind:src="seller.avatar" alt="商店logo">
            </div>
            <div class="content">
                <div class="title">
                    <span class="brand"></span>
                    <span class="name">{{seller.name}}</span>
                </div>
                <div class="description">
                    {{seller.description}}/{{seller.deliveryTime}}分钟送达
                </div>
                <div v-if="seller.supports" class="support">
                     <support :size="28" :support-type="seller.supports[0].type"></support>
                    <span class="text">{{seller.supports[0].description}}</span>
                </div>
            </div>
            <div v-if="seller.supports" class="support-count">
                <span class="count">{{seller.supports.length}}个</span>
                <i class="icon-keyboard_arrow_right" @click="showDetail"></i>
            </div>
        </div>
        <div class="bullentin-wrapper">
            <span class="bulletin-title"></span>
            <span class="bulletin-text">{{seller.bulletin}}</span>
            <i class="icon-keyboard_arrow_right" @click="showDetail"></i>
        </div>
        <div class="background-box">
            <img :src="seller.avatar" alt="背景图">
        </div>
        <div v-show="detailShow" class="detail" transition="fade">
            <div class="detail-wrapper clearfix">
                <div class="detail-main">
                  <h1 class="name">{{seller.name}}</h1>
                    <div class="star-wrapper">
                        <star :size="48" :score="seller.score"></star>
                    </div>
                    <div class="title">
                        <div class="line"></div>
                        <div class="text">优惠信息</div>
                        <div class="line"></div>
                    </div>
                    <ul v-if="seller.supports" class="supports">
                        <li class="support-item" v-for="item in seller.supports">
                            <support :size="32" :support-type="seller.supports[$index].type"></support>
                            <span class="text">{{seller.supports[$index].description}}</span>
                        </li>

                    </ul>
                    <div class="title">
                        <div class="line"></div>
                        <div class="text">商家公告</div>
                        <div class="line"></div>
                    </div>
                    <div class="bulletin">
                        <p class="content">{{seller.bulletin}}</p>
                    </div>
                </div>
            </div>
            <div class="detail-close" @click="hideDetail">
                <i class="icon-close"></i>
            </div>
        </div>
    </header>
</template>

<script type="text/ecmascript-6">
    import star from 'components/star/star';
    import support from 'components/support/support';
        export default{
            props: {
                seller: {
                    type: Object
                }
            },
            data() {
                return {
                    detailShow: false
                };
            },
            methods: {
                showDetail() {
                    this.detailShow = true;
                },
                hideDetail() {
                    this.detailShow = false;
                }

            },

            components: {
                star,
                support
            }

        };
</script>

<style lang='scss' rel="stylesheet scss" scoped>
@import "../../common/scss/mixin";
#header{
    background:rgba(7,17,27,.5);
    position:relative;
    color:#fff;
    overflow:hidden;
    .content-wrapper{
        position:relative;
        padding:1.2rem 0.6rem 0.9rem 1.2rem;
        font-size: 0;
        .avatar,.content{
            display: inline-block;
            font-size:14px;
        }
        .avatar{
            vertical-align:top;
            img{
                width:3.2rem;
                height:3.2rem;
                border-radius:0.1rem;
            }
        }
        .content{
            margin-left:0.8rem;
            .title{
                margin:0.1rem 0 0.4rem 0;
                .brand{
                    display:inline-block;
                    width:1.5rem;
                    height:0.9rem;
                    @include bg-img("brand");
                    background-size:1.5rem 0.9rem;
                    background-repeat: no-repeat;
                    vertical-align:top;
                }
                .name{
                    display:inline-block;
                    vertical-align: top;
                    margin-left:0.3rem;
                    font-size:0.8rem;
                    line-height: 0.9rem;
                    font-weight: bold;
                }
            }
            .description{
                margin-bottom: 0.5rem;
                line-height:0.6rem;
                font-size:12px;
            }
            .support{
                .text{
                    display:inline-block;
                    margin-left: 0.1rem;
                    line-height:0.6rem;
                    font-size:10px;
                }
            }
        }
        .support-count{
            position:absolute;
            right:0.6rem;
            bottom:0.9rem;
            padding:0 0.4rem;
            height:1.2rem;
            line-height:1.2rem;
            border-radius:0.7rem;
            text-align: center;
            background-color:rgba(0,0,0,0.2);
            .count{
                font-size:10px;
                vertical-align:top;
            }
            .icon-keyboard_arrow_right{
                margin-left:0.05rem;
                font-size:10px;
                vertical-align:top;
                line-height:1.2rem;
            }
        }
    }
    .bullentin-wrapper{
        position:relative;
        height:1.4rem;
        padding:0 0.6rem 0 0.6rem;
        font-size:0;
        background-color:rgba(7,17,27,0.2);
        .bulletin-title{
            display:inline-block;
            vertical-align:top;
            margin-top:0.4rem;
            width:1.1rem;
            height:0.6rem;
            @include bg-img('bulletin');
            background-size:1.1rem 0.6rem;
            background-repeat:no-repeat;
        }
        .bulletin-text{
            display:inline-block;
            width:90%;
            line-height:1.4rem;
            vertical-align:top;
            overflow: hidden;
            white-space: nowrap;
            text-overflow:ellipsis;
            margin:0 0.2rem 0 0.2rem;
            font-size:0.5rem;
        }
        .icon-keyboard_arrow_right{
            font-size:0.5rem;
            position:absolute;
            right:0.6rem;
            top:0.4rem;
        }
    }
    .background-box{
        position:absolute;
        top:0;
        right:0;
        bottom:0;
        left: 0;
        z-index: -1;
        filter:blur(10px);
        img{
            width:100%;
            height:100%;
        }
    }

    .detail{
        position:fixed;
        z-index:100;
        top:0;
        left:0;
        width:100%;
        height:100%;
        overflow:auto;
        transition:all 0.5s;
        backdrop-filter:blur(10px);
        &.fade-transition{
            opacity:1;
            background-color:rgba(7,17,27,0.8);
        }
        &.fade-enter,&.fade-leave{
            opacity:0;
            background-color:rgba(7,17,27,0);
        }
        .detail-wrapper{
            width:100%;
            min-height: 100%;
            .detail-main{
                padding-top:3.2rem;
                padding-bottom: 3.2rem;
                .name{
                    line-height:0.8rem;
                    font-size:0.8rem;
                    font-weight:700;
                    text-align: center;
                    letter-spacing: 0.05rem;
                }
                .title{
                    display:flex;
                    width:80%;
                    margin:1.4rem auto 1.2rem auto;
                    .line{
                       flex:1;
                       position:relative;
                       top:-0.3rem;
                       border-bottom:1px solid rgba(255,255,255,0.2);
                    }
                    .text{
                        padding:0 0.6rem ;
                        font-weight:700;
                        font-size:0.7rem;
                        line-height:0.7rem;
                    }
                }
                .star-wrapper{
                    text-align:center;
                    height:1.2rem;
                    margin:0.8rem auto 0 auto;

                }
                .supports{
                    width:80%;
                    margin:0 auto;
                    .support-item{
                        margin-left:1.2rem;
                        margin-bottom:0.6rem;
                        /* .icon{
                            display:inline-block;
                            width:0.8rem;
                            height:0.8rem;
                            background-size:0.8rem 0.8rem;
                            background-repeat:no-repeat;
                        &.decrease{
                            @include bg-img('decrease_2');
                        }
                         &.discount{
                            @include bg-img('discount_2');
                        }&.guarantee{
                            @include bg-img('guarantee_2');
                        }
                         &.invoice{
                            @include bg-img('invoice_2');
                        }
                        &.special{
                            @include bg-img('special_2');
                        }
                        } */
                        .text{
                            display:inline-block;
                            margin-left:0.3rem;
                            vertical-align:top;
                            font-size:0.6rem;
                            line-height:0.6rem;
                        }
                    }
                }
                .bulletin{
                    width:80%;
                    margin:0 auto;
                    .content{
                        padding:0 1.2rem;
                        font-size:0.6rem;
                        line-height:1.2rem;
                    }
                }
            }
        }
        .detail-close{
            position:relative;
            width:1.6rem;
            height:1.6rem;
            margin:-3.2rem auto 0 auto;
            clear:both;
            font-size:1.6rem;
            color:rgba(255,255,255,0.5);
        }
    }
}
</style>