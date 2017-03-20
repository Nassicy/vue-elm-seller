<template>
    <div class="food" v-show="showFlag" transition="move" v-el:food>
        <div class="food-content">
            <div class="image-header">
                <img :src="food.image" alt="" class="img">
                <div class="back" @click="hide">
                  <i class="icon-arrow_lift"></i>
                </div>
            </div>
            <div class="content ">
                <div class="name">{{food.name}}</div>
                <div class="extra">
                    <span class="sell-count">月售{{food.sellCount}}份</span>
                    <span class="rating">好评率{{food.rating}}%</span>
                </div>
                <div class="price-box">
                    <span class="now-price">￥{{food.price}}</span>
                    <span class="old-price" v-show="food.oldPrice">￥{{food.oldPrice}}</span>
                </div>
                <div class="shopcart-wrapper" @click.stop.prevent="addFirst($event)"  v-show="!food.count || food.count==0" transition="fade">
                    <span class="add-cart">加入购物车</span>
                </div>
                <div class="cartcontrol-wrapper" >
                    <cartcontrol :food="food"></cartcontrol>
                </div>
            </div>
            <split v-show="food.info"></split>
            <div class="introduce" v-show="food.info">
                <h2 class="title title-common">商品介绍</h2>
                <div class="content">{{food.info}}</div>
            </div>
            <split></split>
            <div class="ratings">
                <h2 class="title title-common">商品评论</h2>
                <div class="ratings-box">
                    <ratingselect :select-type="selectType" :only-content="onlyContent" :desc="desc" :ratings="food.ratings"></ratingselect>
                </div>
                <div class="rating-wrapper">
                    <ul v-show="food.ratings && food.ratings.length">
                        <li v-for="rating in food.ratings" class="rating-list border-px" v-show="needShow(rating.rateType,rating.text)">
                            <div class="rating-header clearfix">
                                <span class="date">{{rating.rateTime | formatDate}}</span>
                                <div class="user-box">
                                    <span class="name">{{rating.username}}</span>
                                    <img :src="rating.avatar" alt="" class="avatar">
                                </div>
                            </div>
                            <div class="rating-content">
                                <span :class="{'icon-thumb_down':rating.rateType===1,'icon-thumb_up':rating.rateType===0}"></span>
                                <span class="text">{{rating.text}}</span>
                            </div>
                        </li>
                    </ul>
                    <div class="no-ratings" v-show="!food.ratings || !food.ratings.length">暂无评价</div>
                </div>
            </div>
        </div>
    </div>
</template>
<script type="text/ecmascript-6">
    import split from 'components/split/split';
    import cartcontrol from 'components/cartcontrol/cartcontrol';
    import shopcart from 'components/shopcart/shopcart';
    import ratingselect from 'components/ratingselect/ratingselect';
    import {formatDate} from 'common/js/date';
    import BScroll from 'better-scroll';
    import Vue from 'vue';

    const ALL = 2;
    export default {
        props: {
            food: Object
        },
        data() {
            return {
                showFlag: false,
                selectType: ALL,
                onlyContent: true,
                desc: {
                    all: '全部',
                    positive: '推荐',
                    negative: '吐槽'
                }
            };
        },
        components: {
            split,
            shopcart,
            cartcontrol,
            ratingselect
        },
        methods: {
            show() {
                this.showFlag = true;
                this.selectType = ALL;
                this.onlyContent = true;
                this.$nextTick(() => {
                    if (!this.scroll) {
                        this.scroll = new BScroll(this.$els.food, {
                            click: true
                        });
                    } else {
                        this.scroll.refresh();
                    }
                });
            },
            hide() {
                this.showFlag = false;
            },
            addFirst(event) {
                if (!event._constructed) {
                    return;
                }
                Vue.set(this.food, 'count', 1);
                this.$dispatch('cart.add', event.target);
            },
           needShow(type, text) {
                if (this.onlyContent && !text) {
                  return false;
                }
                if (this.selectType === ALL) {
                  return true;
                } else {
                  return type === this.selectType;
                }
            }
        },
        events: {
          'ratingtype.select'(type) {
            this.selectType = type;
            this.$nextTick(() => {
              this.scroll.refresh();
            });
          },
          'content.toggle'(onlyContent) {
            this.onlyContent = onlyContent;
            this.$nextTick(() => {
              this.scroll.refresh();
            });
          }
        },
        filters: {
            formatDate(time) {
                let date = new Date(time);
                return formatDate(date, 'yyyy-MM-dd hh:mm');
            }
        }

    };
</script>
<style lang="scss" scoped>
    @import '../../common/scss/mixin';
    .food{
        position:fixed;
        top:0;
        left:0;
        bottom:2.4rem;
        z-index:30;
        width:100%;
        background-color:#fff;
        &.move-transition{
            transition:all 0.4s linear;
            transform:translate3d(0,0,0);
        }
        &.move-enter,&.move-leave{
            transform:translate3d(100%,0,0);
        }
        .image-header{
            position:relative;
            width:100%;
            height:0;
            padding-top:100%;
            .img{
                position:absolute;
                top:0;
                left:0;
                width:100%;
                height:100%;
            }
            .back{
                position:absolute;
                top:0.5rem;
                left:0;
                .icon-arrow_lift{
                    display:block;
                    padding:0.6rem;
                    color:#fff;
                    background-color:rgba(0,0,0,0.5);
                    border-radius:50%;
                }
            }
        }
        .content{
            padding:0.9rem;
            position:relative;
            .name{
                font-size:0.7rem;
                font-weight:700;
                color:rgb(7,17,27);
                line-height:0.7rem;
            }
            .extra{
                font-size:0;
                color:rgb(147,153,159);
                margin:0.4rem auto 0.9rem auto;
                .sell-count{
                    font-size:0.5rem;
                    line-height:0.5rem;
                    margin-right:0.6rem;
                }
                .rating{
                    line-height:0.5rem;
                    font-size:0.5rem;
                }
            }
            .price-box{
                .now-price{
                    margin-right:0.6rem;
                    font-size:0.5rem/0.7rem;
                    color:rgb(240,20,20);
                    line-height:1.2rem;
                    font-weight:normal/700;
                }
                .old-price{
                    font-size:0.5rem;
                    font-weight:normal/700;
                    color:rgb(147,153,159);
                    line-height:1.2rem;
                    text-decoration:line-through;
                }
            }
            .shopcart-wrapper{
                position:absolute;
                right:0.9rem;
                bottom:0.9rem;
                z-index:10;
                box-sizing:border-box;
                &.fade-transition{
                    transition:all 0.2s;
                    opacity:1;
                }
                &.fade-enter,&.fade-leave{
                    opacity:0;
                }
                .add-cart{
                    display:inline-block;
                    width:3.7rem;
                    height:1.2rem;
                    line-height:1.2rem;
                    padding:0 0.3rem;
                    border-radius:0.6rem;
                    background:rgb(0,160,220);
                    text-align: center;
                    font-size:0.5rem;
                    color:#fff;
                }
            }
            .cartcontrol-wrapper{
                position:absolute;
                right:0.9rem;
                bottom:0.6rem;
            }
        }
        .title-common{
            font-size:0.7rem;
            line-height:0.7rem;
            color: rgb(7, 17, 27);
        }
        .introduce{
            padding:0.9rem;
             font-size:0;
            .title{
                margin-bototm:0.3rem;
            }
            .content{
                font-size:0.6rem;
                font-weight:200;
                color:rgb(77,85,93);
                line-height:1.2rem;
            }
        }
        .ratings{
            padding:0.9rem;
            .rating-wrapper{
                .rating-list{
                    padding:0.8rem 0;
                    @include border-px(rgba(7,17,27,0.1));
                    &:last-child{
                        @include border-none();
                    }
                    .rating-header{
                        color:rgb(147,153,159);
                        .date{
                           display:inline-block;
                           float:left;
                           font-size:0.6rem;
                           line-height:0.6rem;
                        }
                        .user-box{
                            float:right;
                            .name{
                                font-size:0.5rem;
                                line-height:0.6rem;
                                margin-right:0.3rem;
                                vertical-align:top;
                            }
                            .avatar{
                                width:0.6rem;
                                height:0.6rem;
                                border-radius:50%;
                            }
                        }
                    }
                    .rating-content{
                        margin-top:0.3rem;
                        .icon-thumb_down{
                            color:rgb(147,153,159);
                            font-size:0.6rem;
                            line-height:1.2rem;
                        }
                        .icon-thumb_up{
                            color:rgb(0,160,220);
                            font-size:0.6rem;
                            line-height:1.2rem;
                        }
                        .text{
                            margin-left:0.2rem;
                            font-size:0.6rem;
                            line-height:0.8rem;
                            color:rgb(7,17,27);
                        }
                    }
                }
                .no-ratings{
                   padding:0.8rem 0;
                   font-size:0.6rem;
                   color:rgb(147,153,159);
                }
            }
        }
    }
</style>