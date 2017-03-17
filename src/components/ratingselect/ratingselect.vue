<template>
    <div class="ratingselect">
        <div class="rating-type">
            <span class="inline-block all">{{desc.all}}</span>
            <span class="inline-block positive">{{desc.positive}}</span>
            <span class="inline-block negative">{{desc.negative}}</span>
        </div>
        <div class="switch">
            <span class="icon-check_circle"></span>
            <span class="text">只看有内容的评价</span>
        </div>
    </div>
</template>
<script type="text/ecmascript-6">
   /* const POSITIVE = 0;
    const NEGATIVE = 1;*/
    const ALL = 2;
    export default {
        props: {
            ratings: {
                type: Array,
                default() {
                    return [];
                }
            },
            selectType: {
                type: Number,
                default: ALL
            },
            onlyContent: {
                type: Boolean,
                default: false
            },
            desc: {
                type: Object,
                default() {
                    return {
                        all: '全部',
                        positive: '满意',
                        'negative': '不满意'
                    };
                }
            }
        }
    };
</script>
<style lang="scss" scoped>
    .ratingselect{
        .rating-type{
            .inline-block{
                display:inline-block;
                padding:0.4rem 0.6rem;
                font-size:0.6rem/0.4rem;
                border-radius:0.05rem;
            }
            .all{
                background-color:rgb(0,160,220);
                color:#fff;
            }
            .positive{
                margin:0 0.4rem;
                background-color:rgba(0,160,220,0.2);
                color:rgb(77,85,93);
            }
            .negative{
                background-color:rgba(77,85,93,0.2);

            }
        }
    }
</style>