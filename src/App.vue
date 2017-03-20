<template>
  <div id="wrap">
    <v-header v-bind:seller="seller"></v-header>
    <div class="tab border-px">
      <div class="tab-item">
        <a v-link="{path:'/goods'}">商品</a>
      </div>
      <div class="tab-item">
        <a v-link="{path:'/ratings'}">评论</a>
      </div>
      <div class="tab-item">
        <a v-link="{path:'/seller'}">商家</a>
      </div>
    </div>
    <router-view :seller="seller"></router-view>
  </div>
</template>

<script type="text/ecmascript-6">
import header from './components/header/header.vue';

const ERR_OK = 0;

export default{
  data() {
    return {
      seller: {},
      ratings: {}
    };
  },
  created() {
    this.$http.get('/api/seller').then((response) => {
      response = response.body;
      if (response.errno === ERR_OK) {
        this.seller = response.data;
      }
    });
  },

  components: {
    'v-header': header
  }
};
</script>
<style lang="scss"  scoped>
@import 'common/scss/mixin';
  #wrap{
    .tab{
      display:flex;
      width:100%;
      height:2.0rem;
      line-height:2.0rem;
      @include border-px(rgba(7,17,27,0.1));
      .tab-item{
        flex:1;
        text-align:center;
        a{
          display:block;
          font-size:0.7rem;
          color:rgb(77,85,93);
          &.active{
            color:rgb(240,20,20);
          }
        }
      }
    }
  }
</style>
