<template>
    <div class="goods-box">
        <div class="menu-wrapper" v-el:menu-wrapper>
            <ul>
                <li  v-for="item in goods" class="menu-item" :class="{'current':currentIndex===$index}" @click="selectMenu($index,$event)">
                    <span class="text border-px">
                        <span v-show="item.type>0" class="icon-box">
                            <support :size="24" :support-type="item.type"></support>
                        </span>
                        {{item.name}}
                    </span>
                </li>
            </ul>
        </div>
        <div class="foods-wrapper" v-el:foods-wrapper>
            <ul >
                <li  v-for="item in goods" class="goods-item food-list-hook">
                    <h1 class="title">{{item.name}}</h1>
                    <ul class="food-box">
                        <li v-for="food in item.foods" class="food-item border-px" @click="selectFood(food,$event)">
                            <div class="icon-box">
                                <img :src="food.icon" alt="food.name" class="icon">
                            </div>
                            <div class="content">
                                <h2 class="name">{{food.name}}</h2>
                                <p class="info-box description">{{food.description}}</p>
                                <p class="info-box extra">
                                    <span class="sellCount">月售{{food.sellCount}}份</span><span>好评率{{food.rating}}%</span>
                                </p>
                                <div class="price-box">
                                    <span class="currentPrice">￥{{food.price}}</span>
                                    <span v-show="food.oldPrice" class="oldPrice">￥{{food.oldPrice}}</span>
                                </div>
                                <div class="cartcontrol-wrapper">
                                    <cartcontrol :food="food"></cartcontrol>
                                </div>
                            </div>
                        </li>
                    </ul>
                </li>
            </ul>
        </div>
        <shopcart v-ref:shopcart :select-foods="selectFoods":delivery-price="seller.deliveryPrice" :min-price="seller.minPrice"></shopcart>
    </div>
    <food :food="selectedFood" v-ref:food></food>
</template>

<script type="text/ecmascript-6">
    import support from 'components/support/support';
    import shopcart from 'components/shopcart/shopcart';
    import cartcontrol from 'components/cartcontrol/cartcontrol';
    import food from 'components/food/food';
    import BScroll from 'better-scroll';

    const ERR_OK = 0;
    export default{
        props: {
            seller: {
                type: Object
            }
        },
        data() {
            return {
                goods: [],
                listHeight: [],
                scrollY: 0,
                selectedFood: {}
            };
        },
         components: {
            support,
            shopcart,
            cartcontrol,
            food
        },
        created() {
            this.$http.get('/api/goods').then((response) => {
                response = response.body;
                if (response.errno === ERR_OK) {
                    this.goods = response.data;
                    this.$nextTick(() => {
                        this._initScroll();
                        this._calculateHeight();
                    });
                }
            });
        },
        methods: {
            _initScroll() {
                this.menuScroll = new BScroll(this.$els.menuWrapper, {
                    click: true
                });
                this.foodScroll = new BScroll(this.$els.foodsWrapper, {
                    click: true,
                    probeType: 3
                });
                this.foodScroll.on('scroll', (pos) => {
                    this.scrollY = Math.abs(Math.round(pos.y));
                });
            },
            _calculateHeight() {
                let foodList = this.$els.foodsWrapper.getElementsByClassName('food-list-hook');
                let height = 0;
                this.listHeight.push(height);
                for (let i = 0; i < foodList.length; i++) {
                    let item = foodList[i];
                    height += item.clientHeight;
                    this.listHeight.push(height);
                }
            },
            selectMenu(index, event) {
                if (!event._constructed) {
                    return;
                }
                let foodList = this.$els.foodsWrapper.getElementsByClassName('food-list-hook');
                let el = foodList[index];
                this.foodScroll.scrollToElement(el, 300);
            },
            _drop(target) {
                // 体验优化,异步执行下落动画
                this.$nextTick(() => {
                    this.$refs.shopcart.drop(target);
                });
            },
            selectFood(food, event) {
                if (!event._constructed) {
                    return;
                }
                this.selectedFood = food;
                this.$refs.food.show();
            }
        },
        computed: {
            currentIndex() {
                for (let i = 0; i < this.listHeight.length; i++) {
                    let height1 = this.listHeight[i];
                    let height2 = this.listHeight[i + 1];
                    if (!height2 || (this.scrollY >= height1 && this.scrollY < height2)) {
                        return i;
                    }
                }
                return 0;
            },
            selectFoods() {
                let foods = [];
                this.goods.forEach((goods) => {
                    goods.foods.forEach((food) => {
                        if (food.count) {
                            foods.push(food);
                        }
                    });
                });
                return foods;
            }

        },
        events: {
            'cart.add'(target) {
                this._drop(target);
            }
        }

    };
</script>
<style lang="scss"  scoped>
    @import '../../common/scss/mixin';
    .goods-box{
        position:absolute;
        width:100%;
        top:8.8rem;
        bottom:2.3rem;
        overflow:hidden;
        display: flex;
        .menu-wrapper{
            flex:0 0 4.0rem;
            background: #f3f5f7;
            .menu-item{
                display:table;
                width:3.2rem;
                height:2.7rem;
                line-height:0.7rem;
                font-size:0;
                padding:0 0.6rem;
                &:last-child{
                    .text{
                        @include border-none();
                    }
                }
                .text{
                    display:table-cell;
                    width:2.8rem;
                    vertical-align:middle;
                    font-size:0.6rem;
                    @include border-px(rgba(7,17,27,0.1));

                    .icon-box{
                        font-size:0;
                        margin-right:0.1rem;
                       .icon{
                        vertical-align:top;
                       }
                    }
                }
            }
            .current{
                background:#fff;
                position:relative;
                margin-top:-1px;
                z-index:10;
                font-weight:700;
                .text{
                    @include border-none();
                }
            }
        }
        .foods-wrapper{
            background-color:#f3f5f7;
            flex:1;
            .goods-item{
                .title{
                    padding-left:0.7rem;
                    font-size:0.6rem;
                    color:rgb(147,153,159);
                    line-height:1.3rem;
                    height:1.3rem;
                    border-left:0.1rem solid #d9dde1;
                }
                .food-box{
                    background-color:#fff;
                }
                .food-item{
                    margin:0 0.9rem;
                    padding:0.9rem 0;
                    display:flex;
                    @include border-px(rgba(7,17,27,0.1));
                    &:last-child{
                        @include border-none();
                    }
                    .icon-box{
                        flex:0 0 2.85rem;
                        height:2.85rem;
                        margin-right:0.5rem;
                        .icon{
                            width:100%;
                            height:100%;
                            border-radius:0.1rem;
                        }
                    }
                    .content{
                        flex:1;
                        position:relative;
                        color:rgb(167,153,159);
                        .name{
                            margin-top:0.1rem;
                            font-size:0.7rem;
                            line-height:0.7rem;
                            color:rgb(7,17,27);
                        }
                        .info-box{
                            margin-bottom:0.4rem;
                            font-size:0.5rem;
                            line-height:0.5rem;
                        }
                        .description{
                            line-height:0.7rem;
                            margin:0.4rem auto;
                        }
                        .sellCount{
                            margin-right:0.6rem;
                        }
                        .price-box{
                            font-weight:normal/700;
                            line-height:1.2rem;
                            .currentPrice{
                                font-size:0.5rem/0.7rem;
                                color:rgb(240,20,20);
                                margin-right:0.4rem;
                            }
                            .oldPrice{
                                font-size:0.5rem;
                                text-decoration:line-through;
                            }
                        }
                        .cartcontrol-wrapper{
                            position:absolute;
                            right:0;
                            bottom:0;
                        }
                    }
                }
            }
        }
    }
</style>
