<template>
    <div class="seller" v-el:seller>
        <div class="seller-content">
            <div class="seller-header">
                <div class="top">
                    <h1 class="name">{{seller.name}}</h1>
                    <div class="extra">
                        <span class="star-wrapper">
                            <star :size="36" :score="seller.score"></star>
                        </span>
                        <span class="text">({{seller.ratingCount}})</span>
                        <span class="text">月售{{seller.sellCount}}单</span>
                    </div>
                    <div class="favorite" @click="toggleFavorite">
                        <span class="icon-favorite" :class="{'active':favorite}"></span>
                        <span class="text">{{favoriteText}}</span>
                    </div>
                </div>
                <ul class="info">
                    <li class="info-item">
                        <h3 class="title">起送价</h3>
                        <span class="text">{{seller.minPrice}}</span><i class="unit">元</i>
                    </li>
                    <li class="info-item">
                        <h3 class="title">商家配送</h3>
                        <span class="text">{{seller.deliveryPrice}}</span><i class="unit">元</i>
                    </li>
                    <li class="info-item">
                        <h3 class="title">平均配送时间</h3>
                        <span class="text">{{seller.deliveryTime}}</span><i class="unit">分钟</i>
                    </li>
                </ul>
            </div>
            <split></split>
            <div class="bulletin">
                <h1 class="common-title">公告与活动</h1>
                <div class="content border-px">{{seller.bulletin}}</div>
                <ul class="supports">
                    <li v-for="item in seller.supports" class="support-item">
                        <span class="support-wrapper">
                            <support :size="32" :support-type="item.type"></support>
                        </span>
                        <span class="description">{{item.description}}</span>
                    </li>
                </ul>
            </div>
            <split></split>
            <div class="pics">
                <h1 class="common-title">商家实景</h1>
                <div class="pic-wrapper" v-el:pic-wrapper>
                    <ul class="pic-list" v-el:pic-list>
                        <li v-for="pic in seller.pics" class="pics-item">
                            <img :src="pic" width="120px" height="90px" alt=""/>
                        </li>
                    </ul>
                </div>
            </div>
            <split></split>
            <div class="seller-info">
                <h1 class="common-title">商家信息</h1>
                <ul>
                    <li v-for="info in seller.infos" class="info-list">
                        {{info}}
                    </li>
                </ul>
            </div>
        </div>
    </div>
   <!--  <shopcart :delivery-price="seller.deliveryPrice" :min-price="seller.minPrice"></shopcart> -->
</template>

<script type="text/ecmascript-6">
    import BScroll from 'better-scroll';
    import {saveToLocal, loadFromLocal} from 'common/js/store';
    import split from 'components/split/split';
    import star from 'components/star/star';
    import support from 'components/support/support';
    import shopcart from 'components/shopcart/shopcart';
    export default{
        props: {
            seller: {
                type: Object
            }
        },
        data() {
            return {
                favorite: (() => {
                  return loadFromLocal(this.seller.id, 'favorite', false);
                })()
            };
        },
        computed: {
            favoriteText() {
                return this.favorite ? '已收藏' : '收藏';
              }
        },
         watch: {
          'seller'() {
            this._initScroll();
            this._initPics();
          }
        },
        ready() {
          this._initScroll();
          this._initPics();
        },

        methods: {
            toggleFavorite(event) {
                if (!event._constructed) {
                  return;
                }
                this.favorite = !this.favorite;
                saveToLocal(this.seller.id, 'favorite', this.favoriteText);
            },
            _initScroll() {
                if (!this.scroll) {
                  this.scroll = new BScroll(this.$els.seller, {
                    click: true
                  });
                } else {
                  this.scroll.refresh();
                }
              },
            _initPics() {
                if (this.seller.pics) {
                  let picWidth = 120;
                  let margin = 6;
                  let width = (picWidth + margin) * this.seller.pics.length - margin;
                  this.$els.picList.style.width = width + 'px';
                  this.$nextTick(() => {
                    if (!this.picScroll) {
                      this.picScroll = new BScroll(this.$els.picWrapper, {
                        scrollX: true,
                        eventPassthrough: 'vertical'
                      });
                    } else {
                      this.picScroll.refresh();
                    }
                  });
                }
            }
        },
        components: {
            split,
            star,
            support,
            shopcart
        }
    };
</script>
<style lang="scss"  scoped>
    @import '../../common/scss/mixin';
    .seller{
        position: absolute;
        top: 8.7rem;
        bottom: 0;
        left: 0;
        width: 100%;
        overflow: hidden;
        .seller-header{
            padding:0.9rem;
            position: relative;
            .top{
                padding-bottom:0.9rem;
                @include border-px(rgba(7,17,27,0.1));
                .name{
                    font-size:0.7rem;
                    color:rgb(7,17,27);
                    line-height:0.7rem;
                }
                .extra{
                    margin-top:0.4rem;
                    font-size:0;
                    .star-wrapper,.text{
                        display:inline-block;
                        vertical-align:top;
                    }
                    .star-wrapper{
                        margin-right:0.4rem;
                    }
                    .text{
                        margin-right:0.6rem;
                        margin-top:-0.1rem;
                        font-size:0.5rem;
                        color:rgb(77,85,93);
                        line-height:0.9rem;
                    }
                }
                .favorite{
                    position:absolute;
                    right:0;
                    top:0;
                    font-size:0;
                    .icon-favorite{
                        font-size:1.2rem;
                        line-height:1.2rem;
                        color:#d4d6d9;
                        &.active{
                            color:rgb(240,20,20);
                        }
                    }
                    .text{
                        display:block;
                        width: 2.5rem;
                        font-size:0.5rem;
                        color:rgb(77,86,93);
                        line-height:1.2rem;
                    }
                }

            }
            .info{
                display:flex;
                padding:0.9rem 0;
                .info-item{
                    flex:1;
                    text-align:center;
                    border-right:1px solid rgba(7,17,27,0.1);
                    &:last-child {
                        border: none;
                    }
                    .title{
                        font-size:0.5rem;
                        color:rgb(147,153,159);
                        line-height:0.5rem;
                        margin-bottom:0.3rem;
                    }
                    .text{
                        font-size:1.2rem;
                        font-weight:200;
                        color:rgb(7,17,27);
                        line-height:1.2rem;
                    }
                    .unit{
                        font-style:normal;
                    }
                }

            }
        }
        .common-title{
            line-height: 0.7rem;
            color: rgb(7, 17, 27);
            font-size: 0.7rem;
        }
        .bulletin{
            padding:0.9rem ;
            padding-bottom:0;
            .content{
                margin-top:0.4rem;
                padding:0 0.6rem 0.8rem 0.6rem;
                font-size:0.6rem;
                line-height:1.2rem;
                color:rgb(240,20,20);
                font-weight:200;
                @include border-px(rgba(7,17,27,0.1));
            }
            .support-item{
               padding:0.6rem 0.8rem;
               font-size:0;
               @include border-px(rgba(7,17,27,0.1));
               &:last-child {
                    @include border-none();
                }
               .support-wrapper{
                    margin-right:0.3rem;
                    display:inline-block;
               }
               .description{
                    display:inline-block;
                    vertical-align:top;
                    font-size:0.6rem;
                    font-weight:200;
                    color:rgb(7,17,27);
                    line-height:0.8rem;
               }
            }
        }
        .pics{
            padding:0.9rem 0 0.9rem 0.9rem;
            .pic-wrapper{
                margin-top:0.6rem;
                width:100%;
                overflow:hidden;
                white-space:nowrap;
            }
            .pic-list{
                font-size:0;
            }
            .pics-item{
                display:inline-block;
                margin-right:0.3rem;
                &:lat-child{
                    margin-right:0;
                }
            }
        }
        .seller-info{
            padding:0.9rem;
            padding-bottom:0;
            .common-title{
                padding-bottom:0.6rem;
                @include border-px(rgba(7,17,27,0.1));
            }
            .info-list{
                padding:0.8rem 0.6rem;
                font-size:0.6rem;
                font-weight:200;
                color:rgb(7,17,27);
                line-height:0.8rem;
                @include border-px(rgba(7,17,27,0.1));
                &:last-child {
                    border: none;
                    @include border-none();
                }
            }
        }
    }
</style>
