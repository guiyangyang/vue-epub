<template>
    <transition name="slide-down">
        <div class="title-wrapper" v-show='ifTitleAndMenuShow'>
          <div class="left">
            <span class="icon-arrow-left2 icon"></span>
          </div>
          <div class="right">
            <div class="icon-wrapper">
              <span class="icon-cart icon"></span>
            </div>
            <div class="icon-wrapper">
              <span class="icon-user icon"></span>
            </div>
            <div class="icon-wrapper">
              <span class="icon-section icon"></span>
            </div>
          </div>
        </div>
      </transition>
</template>
<script>
export default {
    props:{
        ifTitleAndMenuShow:{
            type:Boolean,
            default:false
        }
    },
    data() {
        return {

        }
    }
}
</script>
<style lang="scss" scoped>
@import '../assets/styles/global.scss';
 .title-wrapper{
    position: absolute;
    left: 0;
    top: 0;
    z-index: 101;
    display: flex;
    width: 100%;
    height: px2rem(48);
    background-color: white;
    box-shadow: 0 px2rem(8) px2rem(8) rgba(0,0,0,0.15);
    .left{
      flex: 0 0 px2rem(60);
      @include center;
    }
    .right{
       flex:1;
       display: flex;
       justify-content: flex-end;
      .icon-wrapper{
       flex:0 0 px2rem(40);
       @include center;
      }
    }
    // &.slide-down-enter,&.slide-down-leave-to{
    //   transform: translate3d(0,-100%,0)
    // }
    // &.slide-down-enter-to,&.slide-down-leave{
    //   transform: translate3d(0,0,0)
    // }
    // &.slide-down-enter-active,&.slide-down-leave-active{
    //   transition: all 0.3s linear;
    // }
  }
</style>


