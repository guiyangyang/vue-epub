<template>
<div class="menu-bar">
    <transition name="slide-up">
        <div class="menu-wrapper"
        :class="{'hide-box-shadow':ifSettingShow || !ifTitleAndMenuShow}"
         v-show="ifTitleAndMenuShow">
        <div class="icon-wrapper">
            <span class="icon-menu icon" @click="showSetting(3)"></span>
        </div>
        <div class="icon-wrapper">
            <span class="icon-sun icon" @click="showSetting(2)"></span>
        </div>
        <div class="icon-wrapper">
            <span class="icon-share icon" @click="showSetting(1)"></span>
        </div>
        <div class="icon-wrapper">
            <span class="icon-a icon" @click="showSetting(0)">A</span>
        </div>
        </div>
    </transition> 
    <transition name="slide-up">
    <div class="setting-wrapper" v-show="ifSettingShow">
        <!-- 字体设置 -->
        <div class="setting-font-size" v-if="showTag === 0">
            <div class="preview"
            :style="{fontSize:fontSizeList[0].fontSize + 'px'}">A</div>
            <div class="select">
                <div class="select-wrapper" 
                v-for="(item,index) in fontSizeList"
                :key="index"
                @click="setFontSize(item.fontSize)">
                    <div class="line"></div>
                    <div class="point-wrapper">
                        <div class="point"
                        v-show="item.fontSize == defaultFontSize">
                            <div class="small-point"></div>
                        </div>
                    </div>
                    <div class="line"></div>
                </div>
            </div>
            <div class="preview"
            :style="{fontSize:fontSizeList[fontSizeList.length - 1].fontSize + 'px'}"
            >A</div>
        </div>
        <!-- 主题设置 -->
        <div class="setting-theme" v-else-if="showTag === 1">
            <div class="setting-theme-item"
            v-for="(item,index) in themeList"
            :key="index">
                <div class="preview" 
                :style="{background:item.style.body.background}"
                :class="{'no-border':item.style.body.background !=='#fff'}"
                @click="setTheme(index)"></div>
                <div class="text"
                :class="{'selected':index === defaultTheme}">{{item.name}}</div>
            </div>
        </div>
        <!-- 进度条 -->
        <div class="setting-progress" v-else-if="showTag === 2">
            <div class="progress-wrapper">
                <input type="range" class="progress"
                max="100"
                min="0"
                step="1"
                @change='onProgressChange($event.target.value)'
                @input="onProgressInput($event.target.value)"
                :value="progress"
                :disabled="!bookAvailable"
                ref="progress"
                >  
            </div>
            <div class="text-wrapper">
                <span>{{bookAvailable? progress + '%' : '加载中...'}}</span>
            </div>

        </div>

    </div>
    </transition>
    <content-view
    :ifShowContent="ifShowContent"
    v-show="ifShowContent"
    :navigation="navigation"
    :bookAvailable="bookAvailable"
    @jumpTo="jumpTo"></content-view>
    <transition name="fade">
        <div class="content-mask"
        v-show="ifShowContent"
        @click="hideContent">

        </div>
    </transition>    

</div>        
</template>
<script>
import ContentView from '@/components/Content'
export default {
    props:{
        ifTitleAndMenuShow:{
            type:Boolean,
            default:false
        },
        fontSizeList:{
            type:Array
        },
        defaultFontSize:{
            type:Number
        },
        themeList:Array,
        defaultTheme:Number,
        bookAvailable:{
           type: Boolean,
           default:false
        },
        navigation:Object
    },
    data() {
        return {
          ifShowContent:false,
          showTag:0,  
          ifSettingShow:false,
          progress:0,

        }
    },
    components:{
        ContentView
    },
    methods:{
        showSetting(tag) {
            this.showTag = tag;
            if(this.showTag === 3){
                this.ifSettingShow = false;
                this.ifShowContent = true;
            }else{
                this.ifSettingShow = true;
            }
        },
        hideSetting() {
            this.ifSettingShow = false;
        },
        setFontSize(fontSize) {
          this.$emit('setFontSize',fontSize)
        },
        setTheme(index) {
          this.$emit('setTheme',index);
        },
        onProgressInput(progress) {
            this.progress = progress;
            this.$refs.progress.style.backgroundSize = `${this.progress}% 100%`;
        },
        onProgressChange(progress) {
            this.$emit('onProgressChange',progress);
        },
        hideContent() {
          this.ifShowContent = false;
        },
        jumpTo(target){
            this.$emit('jumpTo',target);
        }
    }
}
</script>
<style lang="scss" scoped>
@import '../assets/styles/global.scss';
.menu-bar{
    .menu-wrapper{
        position: absolute;
        left: 0;
        bottom: 0;
        z-index: 101;
        display: flex;
        width: 100%;
        height: px2rem(48);
        background-color: white;
        box-shadow: px2rem(8) 0  px2rem(8) rgba(0,0,0,0.15);
        &.hide-box-shadow{
            box-shadow: none;
        }
    .icon-wrapper{
      flex:1;
      @include center;
    }
    // &.slide-up-enter,&.slide-up-leave-to{
    //   transform: translate3d(0,100%,0)
    // }
    // &.slide-up-enter-to,&.slide-up-leave{
    //   transform: translate3d(0,0,0)
    // }
    // &.slide-up-enter-active,&.slide-up-leave-active{
    //   transition: all 0.3s linear;
    // }
  }
    .setting-wrapper{
        position: absolute;
        bottom: px2rem(48);
        left: 0;
        width: 100%;
        height: px2rem(60);
        z-index: 101;
        background-color: white;
        box-shadow:  0 px2rem(-8) px2rem(8) rgba(0,0,0,0.15);
        .setting-font-size{
            display: flex;
            height: 100%;
            .preview{
              flex: 0 0 px2rem(40);
              @include center;
            }
            .select{
              flex:1;
              display: flex;    
             .select-wrapper{
              flex:1;
              display: flex;
              align-items: center;
              &:first-child{
                  .line{
                      &:first-child{
                          border-top: none;
                      }
                  }
              }
              &:last-child{
                  .line{
                      &:last-child{
                          border-top: none;
                      }
                  }
              }
              .line{
                flex:1;
                height: 0;
                border-top:px2rem(1) solid #ccc;
              }
              .point-wrapper{
                position: relative;  
                flex:0 0 0;
                width:0;
                height: px2rem(7);    
                border-left:px2rem(1) solid #ccc;
                .point{
                  position: absolute;  
                  top:px2rem(-8);
                  left:px2rem(-10);
                  width: px2rem(20);
                  height:px2rem(20);
                  border-radius: 50%;
                  background: white;
                  border:px2rem(1) solid #ccc;
                  box-shadow: 0 px2rem(4) px2rem(4) rgba(0,0,0,0.15);
                  @include center;
                  .small-point{
                    width: px2rem(5);
                    height: px2rem(5);
                    background-color: #000;
                    border-radius: 50%;
                  }
                }
              }
            }
            }
            

        }
        .setting-theme{
            display: flex;
            height: 100%;
          .setting-theme-item{
            flex:1;
            display: flex;  
            flex-direction: column;
            padding: px2rem(5);
            box-sizing: border-box;
              .preview{
                flex:1;
                border:px2rem(1) solid #ccc;
                box-sizing: border-box;
                &.no-border{
                    border:none;
                }
              }
              .text{
                flex:0 0 px2rem(20);
                font-size: px2rem(14);
                color:#ccc;
                @include center;
                &.selected{
                  color:#333;    
                }
              }
          }  
        }
        .setting-progress{
            position: relative;
            width: 100%;
            height:100%;
            .progress-wrapper{
                width: 100%;
                -webkit-appearance: none;
                height: px2rem(2);
                background: -webkit-linear-gradient(#999,#999) no-repeat,#ddd;
                background-size: 0 100%;
                &:focus{
                    outline: none;
                }
                &::-webkit-slider-thumb{
                    -webkit-appearance: none;
                    height:px2rem(20);
                    width:px2rem(20);
                    border-radius: 50%;
                    background: white;
                    box-shadow: 0 4px 4px0 rgba(0,0,0,0.15);
                    border:px2rem(1) solid #ddd;
                }
            }
        }
    }
    .content-mask{
        position: absolute;
        top: 0;
        left:0;
        z-index: 101;
        display: flex;
        width:100%;
        height:100%;
        background: rgba(51,51,51,0.8);
    }
}

</style>



