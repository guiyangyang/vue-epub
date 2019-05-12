<template>
  <transition name="slide-right">
      <div class="content">
          <div class="content-wrapper" v-if="bookAvailable">
              <div class="content-item" 
              v-for="(item,index) in navigation.toc" 
              :key="index"
              @click="jumpTo(item.href)">
                  <span class="text">{{item.label}}</span>
              </div>
          </div>
          <div class="empty" v-else>加载中...</div>
      </div>
  </transition>      
</template>
<script>
export default {
    props:{
        ifShowContent:Boolean,
        navigation:Object,
        bookAvailable:Boolean
        
    },
    data() {
        return {

        }
    },
    methods:{
        jumpTo(target) {
            this.$emit('jumpTo',target)
        }
    }
}
</script>
<style lang="scss" scoped>
@import "../assets/styles/global.scss";
.content {
    position: absolute;
    top: 0;
    left: 0;
    z-index: 110;
    height: 100%;
    width: 78%;
    overflow-y: scroll;
    background: #fff;
    .content-wrapper{
        .content-item{
          margin-top:px2rem(14); 
          margin-left:px2rem(14); 
          cursor: pointer;
          font-size: px2rem(16);
          &:hover{
              color: red;
          }
        }
    }
    .empty{
      font-size: px2rem(20);
      @include center;
      
    }
}

</style>


