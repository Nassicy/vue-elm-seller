<template>
    <div class="star-box" :class="starType">
        <span v-for="itemClass in itemClasses" :class="itemClass" class="star-item" track-by="$index"></span>
    </div>
</template>
<script type="text/javascript">
    const LENGTH = 5;
    const CLS_ON = 'on';
    const CLS_HALF = 'half';
    const CLS_OFF = 'off';
    export default {
        props: {
            size: {
                type: Number
            },
            score: {
                type: Number
            }
        },
        computed: {
            starType() {
                return 'star-' + this.size;
            },
            itemClasses() {
                let result = [];
                let score = Math.floor(this.score * 2) / 2;
                let hasDecimal = score % 1 !== 0;
                let integer = Math.floor(score);
                for (let i = 0; i < integer; i++) {
                    result.push(CLS_ON);
                }
                if (hasDecimal) {
                    result.push(CLS_HALF);
                }
                while (result.length < LENGTH) {
                    result.push(CLS_OFF);
                }
                return result;
            }

        }
    };
</script>

<style lang="scss" scoped>
@import "../../common/scss/mixin";
    .star-box{
        font-size:0;
        .star-item{
            display:inline-block;
            background-repeat:no-repeat;
        }
    }
    .star-48{
        .star-item{
            width:1rem;
            height:1rem;
            margin-right:1.1rem;
            background-size:1rem 1rem;
            &:last-child{
                margin-right:0;
            }
            &.on{
                @include bg-img('star48_on');
            }
            &.half{
                @include bg-img('star48_half');
            }
            &.off{
                @include bg-img('star48_off');
            }
        }
    }
    .star-36{
        .star-item{
            width:0.75rem;
            height:0.75rem;
            margin-right:0.55rem;
            background-size:.75rem .75rem;
            &:last-child{
                margin-right:0;
            }
            &.on{
                @include bg-img('star36_on');
            }
            &.half{
                @include bg-img('star36_half');
            }
            &.off{
                @include bg-img('star36_off');
            }
        }
    }
    .star-24{
        .star-item{
            width:0.5rem;
            height:0.5rem;
            margin-right:0.225rem;
            background-size:0.5rem 0.5rem;
            &:last-child{
                margin-right:0;
            }
            &.on{
                @include bg-img('star24_on');
            }
            &.half{
                @include bg-img('star24_half');
            }
            &.off{
                @include bg-img('star24_off');
            }
        }
    }
</style>