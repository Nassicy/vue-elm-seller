<template>
    <span class="icon" :class="[supType,supSize]" ></span>
</template>

<script type="text/ecmascript-6">
export default {
    props: {
        size: {
            type: Number
        },
        supportType: {
            type: Number
       }
    },
    computed: {
       supType() {
        let classMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee'];
        return classMap[this.supportType] + '-' + this.size;
       },
       supSize() {
        return 'support-item-' + this.size;
       }

    }
};
</script>
<style  lang="scss" scoped>
@import "../../common/scss/mixin";
.icon{
    display:inline-block;
    vertical-align:middle;
    background-repeat:no-repeat;
}
.support-item-28{
    width:0.7rem;
    height:0.7rem;
    background-size:0.7rem 0.7rem;
}
.decrease-28{
    @include bg-img('decrease_1');
}
.discount-28{
    @include bg-img('discount_1');
}
.guarantee-28{
    @include bg-img('guarantee_1');
}
.invoice-28{
    @include bg-img('invoice_1');
}
.special-28{
    @include bg-img('special_1');
}
.support-item-32{
    width:0.8rem;
    height:0.8rem;
    background-size:0.8rem 0.8rem;
}
.decrease-32{
    @include bg-img('decrease_2');
}
.discount-32{
    @include bg-img('discount_2');
}
.guarantee-32{
    @include bg-img('guarantee_2');
}
.invoice-32{
    @include bg-img('invoice_2');
}
.special-32{
    @include bg-img('special_2');
}
.support-item-24{
    width:0.6rem;
    height:0.6rem;
    background-size:0.6rem 0.6rem;
}
.decrease-24{
    @include bg-img('decrease_3');
}
.discount-24{
    @include bg-img('discount_3');
}
.guarantee-24{
    @include bg-img('guarantee_3');
}
.invoice-24{
    @include bg-img('invoice_3');
}
.special-24{
    @include bg-img('special_3');
}
</style>
