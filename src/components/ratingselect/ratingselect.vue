<template>
    <div class="ratingselect">
        <div class="rating-type border-px">
            <span @click="select(2,$event)" class="inline-block positive" :class="{'active':selectType===2}">{{desc.all}}
                <span class="count">{{ratings.length}}</span>
            </span>
            <span @click="select(0,$event)" class="inline-block positive" :class="{'active':selectType===0}">{{desc.positive}}
                 <span class="count">{{positives.length}}</span>
            </span>
            <span @click="select(1,$event)" class="inline-block negative" :class="{'active':selectType===1}">{{desc.negative}}
                 <span class="count">{{negatives.length}}</span>
            </span>
        </div>
        <div class="switch" :class="{'on':onlyContent}" @click="toggleContent($event)">
            <span class="icon-check_circle"></span>
            <span class="text">只看有内容的评价</span>
        </div>
    </div>
</template>
<script type="text/ecmascript-6">
    const POSITIVE = 0;
    const NEGATIVE = 1;
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
        },
        computed: {
            positives() {
                return this.ratings.filter((rating) => {
                    return rating.rateType === POSITIVE;
                });
            },
             negatives() {
                return this.ratings.filter((rating) => {
                    return rating.rateType === NEGATIVE;
                });
            }
        },
        methods: {
           select(type, event) {
                if (!event._constructed) {
                  return;
                }
                this.selectType = type;
                this.$dispatch('ratingtype.select', type);
            },
            toggleContent(event) {
                if (!event._constructed) {
                  return;
                }
                this.onlyContent = !this.onlyContent;
                this.$dispatch('content.toggle', this.onlyContent);
            }
        }

    };
</script>
<style lang="scss" scoped>
@import '../../common/scss/mixin';
    .ratingselect{
        .rating-type{
            margin-top:0.3rem;
            padding-bottom:0.9rem;
          /*   font-size:0; */
            @include border-px(rgba(7,17,27,0.1));
            .inline-block{
                display:inline-block;
                padding:0.4rem 0.6rem;
                font-size:0.6rem/0.4rem;
                border-radius:0.05rem;
                color: rgb(77, 85, 93);
                .count{
                    font-size:0.4rem;
                    margin-left:.1rem;
                    line-height:0.4rem;
                }
                &.active{
                    color:#fff;
                }
            }
            .positive{
                margin-right:0.4rem;
                background-color:rgba(0,160,220,0.2);
                color:rgb(77,85,93);
                &.active{
                    background-color:rgb(0,160,220)
                }
            }
            .negative{
                background-color:rgba(77,85,93,0.2);
                &.active{
                    background-color:rgb(77,85,93)
                }
            }
        }
        .switch{
            padding:0.6rem 0 0.9rem 0;
            @include border-px(rgba(7,17,27,0.1));
            &.on{
                .icon-check_circle{
                    color:#00c850;
                }
            }
            .icon-check_circle{
                font-size:1.2rem;
                color:rgb(147,153,159);
                line-height:1.2rem;
            }
            .text{
                font-size:0.6rem;
                color:rgb(147,153,159);
                line-height:1.2rem;
                margin-left:0.2rem;
                display:inline-block;
                vertical-align: top;
            }
        }
    }
</style>