<template>
    <div class="ebook">
      <!-- <transition name="slide-down">
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
      </transition> -->
      <title-bar :ifTitleAndMenuShow='ifTitleAndMenuShow'></title-bar>
        <div class="read-wrapper">
            <div id="read"></div>
            <div class="mask">
              <div class="left" @click="prevPage"></div>
              <div class="center" @click="toggleTitleAndMenu"></div>
              <div class="right" @click="nextPage"></div>
            </div>
        </div>
        <menu-bar ref="menuBar" 
        :ifTitleAndMenuShow='ifTitleAndMenuShow' 
        :fontSizeList='fontSizeList'
        :defaultFontSize='defaultFontSize'
        :themeList='themeList'
        :defaultTheme='defaultTheme'
        :bookAvailable='bookAvailable'
        :navigation='navigation'
        @setFontSize='setFontSize'
        @setTheme='setTheme'
        @onProgressChange='onProgressChange'
        @jumpTo='jumpTo'
        ></menu-bar>
        <!-- <transition name="slide-up">
          <div class="menu-wrapper" v-show="ifTitleAndMenuShow">
            <div class="icon-wrapper">
              <span class="icon-menu icon"></span>
            </div>
            <div class="icon-wrapper">
              <span class="icon-sun icon"></span>
            </div>
            <div class="icon-wrapper">
              <span class="icon-share icon"></span>
            </div>
            <div class="icon-wrapper">
              <span class="icon-a icon">A</span>
            </div>
          </div>
        </transition> -->
    </div>
</template>
<script>
import TitleBar from '@/components/TitleBar';
import MenuBar from '@/components/MenuBar';
import Epub from 'epubjs';
const DOWLOAD_URL = '/static/cyk.epub';
global.ePub = Epub;
export default {
    data() {
      return {
        ifTitleAndMenuShow:false,
        fontSizeList:[
          {fontSize:12},
          {fontSize:14},
          {fontSize:16},
          {fontSize:18},
          {fontSize:20},
          {fontSize:22},
          {fontSize:24}
        ],
        defaultFontSize:16,
        themeList:[
          {
            name:'default',
            style:{
              body:{
                'color':'#000','background':'#fff'
              }
            }
          },
          {
            name:'eye',
            style:{
              body:{
                'color':'#000','background':'#ceeaba'
              }
            }
          },
          {
            name:'night',
            style:{
              body:{
                'color':'#fff','background':'#000'
              }
            }
          },
          {
            name:'gold',
            style:{
              body:{
                'color':'#000','background':'rgb(241,236,226)'
              }
            }
          }
        ],
        defaultTheme:0, 
        //图书是否处于可用状态
        bookAvailable:false,
        navigation:{}
      }
    },
    components:{
      TitleBar,
      MenuBar
    },
    mounted() {
      this.showEpub()
    },
    methods:{
        // 1. 电子书 解析 渲染
        showEpub() {
          //1.1 生成 book
          this.book = new Epub(DOWLOAD_URL)
        //   console.log(this.book)
          //1.2 生成 rendition
          this.rendition = this.book.renderTo('read',{
            width:window.innerWidth,
            height:window.innerHeight
          })
          //1.3 通过 rendition.display 渲染 电子书
          this.rendition.display()
          //1.4 获取 theme 对象
          this.themes = this.rendition.themes
          //设置 默认字体
          this.setFontSize(this.defaultFontSize)
          //1.5 自定义主题
          // this.themes.register(name,style) //注册 自定义 主题
          // this.themes.select(name)   // 选择 主题
          this.registerTheme();//注册 主题
          // this.themes.select('eye');// 选择 主题
          this.setTheme(this.defaultTheme);// 选择 主题
          //1.6 获取 locations 对象 通过epub函数实现
          this.book.ready.then(() => {
            this.navigation = this.book.navigation;
            return this.book.locations.generate();
          }).then(result => {
            this.locations = this.book.locations;
            // this.onProgressChange(100);
            this.bookAvailable = true;
          })
          
        },
        prevPage() {
          if(this.rendition){
            this.rendition.prev();
          }
        },
        nextPage() {
          if(this.rendition){
            this.rendition.next();
          }
        },
        toggleTitleAndMenu() {
          this.ifTitleAndMenuShow = !this.ifTitleAndMenuShow;
          if(!this.ifTitleAndMenuShow){
            this.$refs.menuBar.hideSetting();
          }
        },
        setFontSize(fontSize) {
          this.defaultFontSize = fontSize;
          if(this.themes){
            this.themes.fontSize(fontSize + 'px');
          }
        },
        registerTheme() {
          this.themeList.forEach(theme => {
            this.themes.register(theme.name,theme.style)
          })
        },
        setTheme(index) {
          this.themes.select(this.themeList[index].name);
          this.defaultTheme = index;
        },
        onProgressChange(progerss) {
          const percentage = progerss / 100 ;
          const location = percentage>0? this.locations.cfiFromPercentage(percentage) : 0 ;
          this.rendition.display(location);
        },
        //章节 跳转
        jumpTo(href) {
          this.rendition.display(href);
          this.hideTitleAndMenu()
        },
        hideTitleAndMenu() {
          this.ifTitleAndMenuShow = false;
          this.$refs.menuBar.hideSetting();
          this.$refs.menuBar.hideContent();
        }

    }
}
</script>
<style lang="scss" scoped>
@import 'assets/styles/global.scss';

.ebook{
  position: relative;
  // .title-wrapper{
  //   position: absolute;
  //   left: 0;
  //   top: 0;
  //   z-index: 101;
  //   display: flex;
  //   width: 100%;
  //   height: px2rem(48);
  //   background-color: white;
  //   box-shadow: 0 px2rem(8) px2rem(8) rgba(0,0,0,0.15);
  //   .left{
  //     flex: 0 0 px2rem(60);
  //     @include center;
  //   }
  //   .right{
  //      flex:1;
  //      display: flex;
  //      justify-content: flex-end;
  //     .icon-wrapper{
  //      flex:0 0 px2rem(40);
  //      @include center;
  //     }
  //   }
  //   // &.slide-down-enter,&.slide-down-leave-to{
  //   //   transform: translate3d(0,-100%,0)
  //   // }
  //   // &.slide-down-enter-to,&.slide-down-leave{
  //   //   transform: translate3d(0,0,0)
  //   // }
  //   // &.slide-down-enter-active,&.slide-down-leave-active{
  //   //   transition: all 0.3s linear;
  //   // }
  // }
  .mask{
    position: absolute;
    top: 0;
    left: 0;
    z-index: 100;
    display: flex;
    width: 100%;
    height: 100%;
    // background-color: yellow;
    .left{
      flex:0 0 px2rem(100);
      // background-color: yellow;
    }
    .center{
      flex:1;
      // background-color: red;
    }
    .right{
      flex:0 0 px2rem(100);
      // background-color: orange;
    }

  }

  // .menu-wrapper{
  //   position: absolute;
  //   left: 0;
  //   bottom: 0;
  //   z-index: 101;
  //   display: flex;
  //   width: 100%;
  //   height: px2rem(48);
  //   background-color: white;
  //   box-shadow: px2rem(8) 0  px2rem(8) rgba(0,0,0,0.15);
  //   .icon-wrapper{
  //     flex:1;
  //     @include center;
  //   }
  //   // &.slide-up-enter,&.slide-up-leave-to{
  //   //   transform: translate3d(0,100%,0)
  //   // }
  //   // &.slide-up-enter-to,&.slide-up-leave{
  //   //   transform: translate3d(0,0,0)
  //   // }
  //   // &.slide-up-enter-active,&.slide-up-leave-active{
  //   //   transition: all 0.3s linear;
  //   // }
  // }
}
</style>

