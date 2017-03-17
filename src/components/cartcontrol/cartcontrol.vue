<template>
    <div class="cartcontrol">
        <span class="cart-decrease move-transition " v-show="food.count>0"  @click.stop.prevent="decreaseCart" transition="move">
        <span class="inner icon-remove_circle_outline "></span>
        </span>
        <span class="cart-count" v-show="food.count>0" >{{food.count}}</span>
        <span class="cart-add icon-add_circle"  @click.stop.prevent="addCart"></span>
    </div>
</template>

<script type="text/ecmascript-6">
import Vue from 'vue';
export default {
    props: {
        food: {
            type: Object
        }
    },
    methods: {
        addCart(event) {
            if (!event._constructed) {
                return;
            }
            if (!this.food.count) {
                Vue.set(this.food, 'count', 1);
            } else {
                this.food.count ++;
            }
            this.$dispatch('cart.add', event.target);
        },
        decreaseCart(event) {
            if (!event._constructed) {
                return;
            }
            if (this.food.count) {
                this.food.count --;
            }
        }
    }

};
</script>

<style  lang="scss" scoped>
    .cartcontrol{
        font-size:0;
        .cart-decrease,{
            display:inline-block;
            padding:0.3rem;
            transition:all 0.4s linear;
            &.move-transition{
                opacity:1;
                transform:translate3d(0,0,0);
                .inner{
                    display:inline-block;
                    font-size:1.2rem;
                    color:rgb(0,160,220);
                    line-height:1.2rem;
                    transition:all 0.4s linear;
                    transform:rotate(0);
                }
            }
            &.move-enter,&.move-leave{
                opacity:0;
                transform:translate3d(1.2rem,0,0);
                .inner{
                    transform: rotate(180deg)
                }
            }
        }
        .cart-count{
            display:inline-block;
            vertical-align:top;
            font-size:0.5rem;
            color:rgb(147,153,159);
            padding-top:0.6rem;
            line-height:0.6rem;
            text-align:center;
        }
        .cart-add{
            display:inline-block;
            padding:0.3rem;
            font-size:1.2rem;
            line-height:1.2rem;
            color:rgb(0,160,220);
        }
    }
</style>
