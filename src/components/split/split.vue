<template>
    <div class="split border-px"></div>
</template>
<script></script>
<style lang="scss" scope>
@import '../../common/scss/mixin';
    .split{
        height:0.8rem;
        width:100%;
        background-color:#f3f5f7;
        @include border-px(rgba(7,17,27,0.1));
        border-top:1px solid rgba(7,17,27,0.1);
    }
</style>