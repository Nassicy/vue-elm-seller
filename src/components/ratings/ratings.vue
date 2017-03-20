<template>
  <div class="ratings" v-el:ratings>
    <div class="rating-header">
        <div class="left">
            <h1 class="score">{{seller.score}}</h1>
            <div class="title">综合评分</div>
            <div class="ranking">高于周边商家{{seller.rankRate}}%</div>
        </div>
        <div class="right">
            <ul>
                <li class="score-wrapper">
                    <span class="title">服务态度</span>
                    <span class="star-wrapper">
                        <star :size="36" :score="seller.serviceScore"></star>
                    </span>
                    <span class="score">{{seller.serviceScore}}</span>
                </li>
                 <li class="score-wrapper">
                    <span class="title">商家评分</span>
                    <span class="star-wrapper">
                        <star :size="36" :score="seller.foodScore"></star>
                    </span>
                    <span class="score">{{seller.foodScore}}</span>
                </li>
                 <li class="score-wrapper">
                    <span class="title">送达时间</span>
                    <span class="time">{{seller.deliveryTime}}分钟</span>
                </li>
            </ul>
        </div>
    </div>
    <split></split>
    <div class="rating-content">
        <div class="rating-wrapper">
            <ratingselect :select-type="selectType" :ratings="ratings" :only-content="onlyContent" :desc="desc"></ratingselect>
        </div>
        <ul>
            <li v-for="rating in ratings" class="rating-item" v-show="needShow(rating.rateType,rating.text)">
                <div class="user clearfix">
                    <div class="left">
                        <div class="avatar">
                            <img :src="rating.avatar" alt="" class="img">
                        </div>
                        <div class="name">
                            <span class="text">{{rating.username}}</span>
                            <span class="star-wrapper">
                                <star :size="24" :score="rating.score"></star>
                            </span>
                            <span class="time" v-show="rating.deliveryTime">{{rating.deliveryTime}}分钟送达</span>
                        </div>
                        <div class="date">{{rating.rateTime | formatDate}}</div>
                    </div>
                </div>
                <p class="content">{{rating.text}}</p>
                <div class="recommend" v-show="rating.recommend && rating.recommend.length">
                    <span class="icon-thumb_up"></span>
                    <span class="item" v-for="item in rating.recommend">{{item}}</span>
              </div>
            </li>
        </ul>
    </div>
  </div>
</template>

<script type="text/ecmascript-6">
    import BScroll from 'better-scroll';
    import split from 'components/split/split';
    import star from 'components/star/star';
    import ratingselect from 'components/ratingselect/ratingselect';
    import {formatDate} from 'common/js/date';
    const ALL = 2;
    const ERR_OK = 0;
    export default{
        props: {
            seller: {
                type: Object
            }
        },
        data() {
            return {
                selectType: ALL,
                ratings: [],
                onlyContent: true
            };
        },
        components: {
            split,
            star,
            ratingselect
        },
        created() {
            this.$http.get('/api/ratings').then((response) => {
                response = response.body;
                if (response.errno === ERR_OK) {
                    this.ratings = response.data;
                    this.$nextTick(() => {
                        this.scroll = new BScroll(this.$els.ratings, {
                            click: true
                        });
                    });
                }
            });
        },
        filters: {
            formatDate(time) {
                let date = new Date(time);
                return formatDate(date, 'yyyy-MM-dd hh:mm');
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
        methods: {
            needShow(type, text) {
                if (this.onlyContent && !text) {
                    return false;
                }
                if (this.selectType === ALL) {
                    return true;
                } else {
                    return this.selectType === type;
                }
            }

        }
    };
</script>
<style lang="scss"  scoped>
    @import '../../common/scss/mixin';
    .ratings{
        .rating-header{
            display:flex;
            padding:0.9rem 0;
            .left{
                flex:0 0 6.875rem;
                width:6.875rem;
                padding:0.6rem 0.3rem 0.6rem 0;
                text-align:center;
                border-right:1px solid rgba(7,17,27,0.1);
                @media only screen and (max-width: 320px){
                    flex:0 0 6rem;
                    width:6rem;
                    padding:0.6rem 1.2rem;
                }
                .score{
                    font-size:1.2rem;
                    color:rgb(255,153,0);
                    line-height:1.4rem;
                }
                .title{
                    margin:0.3rem auto 0.4rem auto;
                    font-size:0.6rem;
                    color:rgb(7,17,27);
                    line-height:0.6rem;
                }
                .ranking{
                    font-size:0.5rem;
                    color:rgb(147, 153, 159);
                    line-height:0.5rem;
                    white-space: nowrap;
                }
            }

            .right{
                flex:1;
                padding:0.3rem 0 0.3rem 1.2rem;
                 @media only screen and (max-width: 320px){
                    padding-left:0.3rem;
                }
                .score-wrapper{
                    font-size:0;
                    margin-bottom:0.4rem;
                    .title{
                        display:inline-block;
                        font-size:0.6rem;
                        line-height:0.6rem;
                        color:rgb(7,17,27);
                        margin-right:0.4rem;
                    }
                    .star-wrapper{
                        display:inline-block;
                        vertical-align:top;
                    }
                    .score{
                        display:inline-block;
                        margin-left:0.4rem;
                        font-size:0.6rem;
                        color:rgb(255,153,0);
                        line-height:0.9rem;
                    }
                    .time{
                        font-size:0.6rem;
                        color:rgb(147,153,159);
                        line-height:0.9rem;
                    }
                }
            }
        }
        .rating-content{
            .rating-wrapper{
                padding:0.9rem 1.2rem;
            }
        }
        .rating-item{
            padding:0.9rem;
            @include border-px(rgba(7,17,27,0.1));
            .user{
                position:relative;
                .left{
                    float:left;
                    display:flex;
                    .avatar{
                        flex:0 0 1.4rem;
                        width:1.4rem;
                        height:1.4rem;
                        .img{
                            width:1.4rem;
                            height:1.4rem;
                            border-radius:50%;
                        }
                    }
                    .name{
                        flex:1;
                        margin-left:0.6rem;
                        .text{
                           display:block;
                           font-size:0.5rem;
                           color:rgb(7,17,27);
                           line-height:0.6rem;
                        }
                        .time,.star-wrapper{
                            display:inline-block;
                        }
                        .time{
                            margin-left:0.3rem;
                            font-size:0.5rem;
                            font-weight:200;
                            color:rgb(147,153,159);
                            line-height:0.6rem;
                        }
                    }
                }
                .date{
                    position:absolute;
                    right:0.9rem;
                    font-size:0.5rem;
                    line-height:0.6rem;
                    font-weight:200;
                    color:rgb(147,153,159);
                }
            }
            .content{
                margin:0.3rem 1.0rem 0.4rem 2rem;
                font-size:0.6rem;
                color:rgb(7,17,27);
                line-height:0.9rem;
            }
            .recommend{
                font-size:0;
                margin:0.4rem auto 0 2rem;
                .icon-thumb_up{
                    display:inline-block;
                    vertical-align:middle;
                    margin-right:0.4rem;
                    color:rgb(0,160,220);
                    font-size:0.6rem;
                    line-height:0.8rem;
                }
                .item{
                    display:inline-block;
                    vertical-align:middle;
                    margin-right:0.4rem;
                    margin-bottom:0.2rem;
                    padding:0.1rem 0.3rem;
                    border:1px solid rgba(7,17,27,0.1);
                    border-radius:0.05rem;
                    background-color:#fff;
                    font-size:0.45rem;
                    color:rgb(147,153,159);
                    line-height:0.8rem;
                  /*   width:2.5rem;
                    white-space:nowrap;
                    text-overflow:ellipsis;
                    overflow:hidden; */
                }
            }
        }
    }
</style>
