<template>
    <footer class="shopcart">
        <div class="content" @click="toggleList">
            <div class="content-left">
                <div class="logo-wrapper">
                    <div class="logo" :class="{'highlight':totalCount>0}">
                        <i class="icon-shopping_cart"></i>
                    </div>
                    <div class="num" v-show="totalCount>0">{{totalCount}}</div>
                </div>
                <div class="price" :class="{'hightlight':totalPrice>0}">￥{{totalPrice}}</div>
                <div class="desc">另需配送费￥{{deliveryPrice}}元</div>
            </div>
            <div class="content-right" @click.stop.prevent="pay">
                <div class="pay" :class="payClass">
                    {{payDesc}}
                </div>
            </div>
        </div>
        <div class="ball-container">
            <div v-for="ball in balls" v-show="ball.show" class="ball" transition="drop">
                <div class="inner inner-hook">

                </div>
            </div>
        </div>
        <div class="shopcart-list" v-show="listShow" transition="fold" >
            <div class="shopcart-header">
                <span class="title">购物车</span>
                <span class="empty" @click="empty">清空</span>
            </div>
            <div class="shopcart-content" v-el:list-content>
                <ul>
                    <li v-for="food in selectFoods" class="food-list">
                        <span class="name">{{food.name}}</span>
                        <span class="price">{{food.price*food.count}}</span>
                        <span class="cartcontrol-wrapper">
                            <cartcontrol :food="food"></cartcontrol>
                        </span>
                    </li>
                </ul>
            </div>
        </div>
    </footer>
    <div class="list-mark" v-show="listShow" transition="fade" @click="hideList"></div>
</template>

<script type="text/ecmascript-6">
    import BScroll from 'better-scroll';
    import cartcontrol from 'components/cartcontrol/cartcontrol';
    export default {
        props: {
            selectFoods: {
                type: Array,
                default() {
                    return [

                    ];
                }
            },
            deliveryPrice: {
                type: Number
            },
            minPrice: {
                type: Number
            }
        },
        components: {
            cartcontrol
        },
        data() {
            return {
                balls: [
                  {
                    show: false
                  },
                  {
                    show: false
                  },
                  {
                    show: false
                  },
                  {
                    show: false
                  },
                  {
                    show: false
                  }
                ],
                dropBalls: [],
                fold: true
            };
        },
        computed: {
            totalPrice() {
                let total = 0;
                this.selectFoods.forEach((food) => {
                    total += food.price * food.count;
                });
                return total;
            },
            totalCount() {
                let count = 0;
                this.selectFoods.forEach((food) => {
                    count += food.count;
                });
                return count;
            },
            payDesc() {
                if (this.totalPrice === 0) {
                    return `￥${this.minPrice}元起送`;
                } else if (this.totalPrice < this.minPrice) {
                    let diff = this.minPrice - this.totalPrice;
                    return `还差￥${diff}元起送`;
                } else {
                    return '去结算';
                }
            },
            payClass() {
                if (this.totalPrice < this.minPrice) {
                    return 'not-enough';
                } else {
                    return 'enough';
                }
            },
            listShow() {
                if (!this.totalCount) {
                    this.fold = true;
                    return false;
                }
                let show = !this.fold;
                if (show) {
                    this.$nextTick(() => {
                        if (!this.scroll) {
                            this.scroll = new BScroll(this.$els.listContent, {
                                click: true
                            });
                        } else {
                            this.scroll.refresh();
                        }
                    });
                }
                return show;
            }

        },
        methods: {
            drop(el) {
                for (let i = 0; i < this.balls.length; i++) {
                    let ball = this.balls[i];
                    if (!ball.show) {
                        ball.show = true;
                        ball.el = el;
                        this.dropBalls.push(ball);
                        return;
                    }
                }
            },
            toggleList() {
                if (!this.totalCount) {
                    return;
                }
                this.fold = !this.fold;
            },
            empty() {
                this.selectFoods.forEach((food) => {
                    food.count = 0;
                });
            },
            hideList() {
                this.fold = true;
            },
            pay() {
                if (this.totalPrice < this.minPrice) {
                    return;
                }
                window.alert(`支付${this.totalPrice}元`);
            }
        },
        transitions: {
            drop: {
                beforeEnter(el) {
                    let count = this.balls.length;
                    while (count--) {
                        let ball = this.balls[count];
                        if (ball.show) {
                            let rect = ball.el.getBoundingClientRect();
                            let x = rect.left - 32;
                            let y = -(window.innerHeight - rect.top - 22);
                            el.style.display = '';
                            el.style.webkitTransform = `translate3d(0,${y}px,0)`;
                            el.style.transform = `translate3d(0,${y}px,0)`;
                            let inner = el.getElementsByClassName('inner-hook')[0];
                            inner.style.webkitTransform = `translate3d(${x}px,0,0)`;
                            inner.style.transform = `translate3d(${x}px,0,0)`;
                        }
                    }
                },
                enter(el) {
                    /*eslint-disable no-unused-vars*/
                    let rf = el.offsetHeight;
                    this.$nextTick(() => {
                        el.style.webkitTransform = 'translate3d(0,0,0)';
                        el.style.transform = 'translate3d(0,0,0)';
                        let inner = el.getElementsByClassName('inner-hook')[0];
                        inner.style.webkitTransform = 'translate3d(0,0,0)';
                        inner.style.transform = 'translate3d(0,0,0)';
                    });
                },
                afterEnter(el) {
                    let ball = this.dropBalls.shift();
                    if (ball) {
                        ball.show = false;
                        el.style.display = 'none';
                    }
                }
            }
        }
    };

</script>

<style  lang="scss" scoped>
    @import '../../common/scss/mixin';
    .shopcart{
        position:fixed;
        bottom:0;
        left:0;
        width:100%;
        z-index:50;
        height:2.4rem;
        background-color:#141d27;
        .content{
            display:flex;
            color:rgba(255,255,255,0.4);
            .content-left{
                flex:1;
                .logo-wrapper{
                    margin:0 0.9rem;
                    padding:0.3rem;
                    display:inline-block;
                    position:relative;
                    top:-0.5rem;
                    width:2.8rem;
                    height:2.8rem;
                    vertical-align:top;
                    background-color:#141d27;
                    border-radius:50%;
                    box-sizing:border-box;
                    .logo{
                        width:100%;
                        height:100%;
                        border-radius:50%;
                        background-color:#2b343c;
                        text-align:center;
                        .icon-shopping_cart{
                            line-height:2.4rem;
                            font-size: 1.2rem;
                            color: #80858a;
                        }
                        &.highlight{
                            background-color:#00a0dc;
                            .icon-shopping_cart{
                                color:#fff;
                            }
                        }
                    }
                    .num{
                        position:absolute;
                        top:0;
                        left:1.5rem;
                        width:1.2rem;
                        border-radius:0.3rem;
                        line-height:0.8rem;
                        color:#fff;
                        text-align:center;
                        font-size:0.45rem;
                        font-weight:700;
                        background-color:rgb(240,20,20);
                        box-shadow:0 0.1rem 0.2rem 0 rgba(0,0,0,0.4);
                    }
                }
                .price{
                    display:inline-block;
                    margin-top:0.6rem;
                    padding-right:0.6rem;
                    line-height:1.2rem;
                    font-size:0.8rem;
                    font-weight:700;
                    border-right:1px solid rgba(255,255,255,0.1);
                    &.hightlight{
                        color:#fff;
                    }
                }
                .desc{
                    display:inline-block;
                    padding-left:0.6rem;
                    line-height:1.2rem;
                    font-size:0.5rem;
                    font-weight:700;
                }
            }
            .content-right{
               flex:0 0 5.25rem;
               width:5.25rem;
               background-color:#2b333b;
               .pay{
                height:2.4rem;
                line-height:2.4rem;
                font-weight:700;
                color:rgba(255,255,255,0.4);
                text-align:center;
                &.not-enough{
                    background: #2b333b;
                }
                &.enough{
                    background: #00b43c;
                    color: #fff;
                }
               }
            }
        }
        .ball-container{
            .ball{
                position:fixed;
                left:1.6rem;
                bottom:1.1rem;
                z-index:200;
                &.drop-transition{
                    transition:all 0.4s cubic-bezier(0.49,-0.29,0.75,0.41);
                    .inner{
                        width:0.8rem;
                        height:0.8rem;
                        border-radius:50%;
                        background-color:rgb(0,160,220);
                        transition:all 0.4s linear;
                    }
                }
            }
        }
        .shopcart-list{
            position:absolute;
            top:0;
            left:0;
            z-index:-1;
            width:100%;
            background-color:#fff;
            &.fold-transition{
                transition:all 0.5s;
                transform:translate3d(0,-100%,0);
            }
            &.fold-enter,&.fold-leave{
                transform:translate3d(0,0,0);
            }
            .shopcart-header{
                height:2.0rem;
                background-color:#f3f5f7;
                padding:0 0.9rem;
                @include border-px(rgba(7,17,27,0.1));
                .title{
                    float:left;
                    font-size:0.7rem;
                    font-weight:200;
                    color:rgb(7,17,27);
                    line-height:2.0rem;
                }
                .empty{
                    float:right;
                    font-size:0.6rem;
                    color:rgb(0,160,220);
                    line-height:2.0rem;
                }
            }
            .shopcart-content{
                padding:0 0.9rem;
                overflow:hidden;
                max-height:13.35rem;
                .food-list{
                    position:relative;
                    height:2.4rem;
                    box-sizing:border-box;
                     @include border-px(rgba(7,17,27,0.1));
                    .name{
                        float:left;
                        padding-top:0.6rem;
                        font-size:0.7rem;
                        color:rgb(7,17,27);
                        line-height:1.2rem;
                    }
                    .price{
                        position:absolute;
                        right:4.5rem;
                        top:0.6rem;
                        font-size:0.5rem/0.7rem;
                        font-weight:normal/700;
                        color:rgb(240,20,20);
                        line-height:1.2rem;
                    }
                    .cartcontrol-wrapper{
                        position:absolute;
                        top:0.2rem;
                        right:0;
                    }
                }
            }
        }
    }
    .list-mark{
        position:fixed;
        top:0;
        left:0;
        width:100%;
        height:100%;
        z-index:40;
        &.fade-transition{
            opacity:1;
            background-color:rgba(7,17,27,0.6);
            transition:all 0.5s;
            backdrop-filter:blur(10px);
        }
        &.fade-enter,&.fade-leave{
            opacity:0;
            background:rgba(7,17,27,0);
        }
    }
</style>