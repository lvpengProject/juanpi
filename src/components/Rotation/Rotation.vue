<template>
    <div >
        <div class="swiper">
            <div class="container">
                <ul
                ref="imgBox"
                @transitionend="animateEnd"
                @touchstart="touchstart"
                @touchmove="touchmove"
                @touchend="touchend">
                <!-- 为了实现无缝循环滚动需要在li前后分别复制最后一张图片和第一张图片 -->
                <li><img :src="imgArr[imgArr.length-1].src" alt=""></li>
                <li v-for="(item, index) in imgArr" :key="index">
                  <img :src="item.src" alt="">
                </li>
                <li><img :src="imgArr[0].src" alt=""></li>
                </ul>
            </div>
            <div class="circle">
                <ol>
                <li
                v-for="(item, index) in imgArr"
                :key="index"
                :class="{current: index===circleIndex}">
                </li>
                </ol>
            </div>
        </div>
    </div>
</template>

<script>
import banner1 from './banner-1.jpg'
import banner2 from './banner-2.jpg'
import banner3 from './banner-3.jpg'
import banner4 from './banner-4.png'
    export default {
        name: 'Pic',
        data() {
            return {
                imgArr: [
                {src: banner1 },
                {src: banner2},
                {src: banner3},
                {src: banner4}
                ],
                flag: true,   // 节流阀 防止快速滑动
                circleIndex: 0, // 当前展示图片对应圆点
                imgIndex: 0,   // 当前展示图片
                timer: null,   // 圆点定时器
                imgWidth: 0,   // 图片宽度
                startX: 0,    // 手指开始触摸位置
                moveX: 0,     // 手指移动距离
                interval: 2000  // 滚动间隔时间
            }
        },
        methods: {
            animateEnd(){   // 过渡结束事件
            if(this.imgIndex >= this.imgArr.length){ // 判断当前图片索引是否为最后一张图
                this.imgIndex = 0;
                this.$refs.imgBox.style.transition = 'none';  // 迅速跳转至第一张图
                this.$refs.imgBox.style.transform = `translateX(-${this.imgWidth}px)`;
            }
            if(this.imgIndex < 0){   // 判断当前图片索引是否为第一张图
                this.imgIndex = this.imgArr.length-1;
                this.$refs.imgBox.style.transition = 'none'; // 迅速跳转至最后一张图
                this.$refs.imgBox.style.transform = `translateX(${this.translateX}px)`;
            }
            this.flag = true; // 打开节流阀
            },
            touchstart(event){  // 手指开始触摸事件
            clearInterval(this.timer);  // 关闭自动轮播
            if(this.flag){
                this.startX = event.targetTouches[0].clientX; // 获取开始触摸位置
            }
            },
            touchmove(event){
            if(this.flag){
                this.moveX = event.targetTouches[0].clientX - this.startX;  // 手指移动位置
                this.$refs.imgBox.style.transition = 'none';   // 图片ul跟随手指移动
                this.$refs.imgBox.style.transform = `translateX(${this.translateX + this.moveX}px)`;
            }
            },
            touchend(event){
            if(this.flag){
                if(this.moveX > 80){  // 移动距离大于80，图片索引--
                this.imgIndex--;
                this.circleIndex--;
                } else if(this.moveX < -80){  // 移动距离小于-80，图片索引++
                this.imgIndex++;
                this.circleIndex++;
                }
                this.$refs.imgBox.style.transition = 'all .5s'; // 展示当前索引图片
                this.$refs.imgBox.style.transform = `translateX(${this.translateX}px)`;
            }
            // 触摸事件完成，继续自动轮播
                clearInterval(this.timer);  // 防止定时器叠加
                this.timer = setInterval(()=>{
                    this.imgIndex++;
                    this.circleIndex++;
                    this.$refs.imgBox.style.transition = 'all .5s';
                    this.$refs.imgBox.style.transform = `translateX(${this.translateX}px)`;
                }, this.interval);
                this.flag = false;  // 关闭节流阀，等待动画完成
            }
        },
      watch: {
        circleIndex(){  // 监视原点索引变化
          if(this.circleIndex >= this.imgArr.length){
            this.circleIndex = 0;
          }
          if(this.circleIndex < 0){
            this.circleIndex = this.imgArr.length-1;
          }
        }
      },
      computed: {
        translateX(){ // 计算图片ul当前距离
          return -(this.imgWidth + this.imgWidth * this.imgIndex);
        }
      },
      mounted() {
        this.imgWidth = window.innerWidth;  // 获取图片宽度
        // ul先左移动一个图片距离，显示第一张图片
        this.$refs.imgBox.style.transform = `translateX(-${this.imgWidth}px)`;
        this.timer = setInterval(()=>{  // 自动轮播
          this.imgIndex++;
          this.circleIndex++;
          this.$refs.imgBox.style.transition = 'all .5s'; // 切换下一张图片
          this.$refs.imgBox.style.transform = `translateX(${this.translateX}px)`;
        }, this.interval)
      }
    }
</script>

<style>
    * {
      margin: 0;
      padding: 0;
    }
    ul,
    ol {
      list-style: none;
    }
    .swiper {
      width: 100%;
      height: 180px;
      background-color: #f40068;
      overflow: hidden;
    }
    .container {
      width: 100%;
      height: 100%;
    }
    .container ul {
      width: 600%;
      height: 100%;
      display: flex;
    }
    .container li {
      width: 25%;
      height: 100%;
    }
    .container li img {
      width: 100%;
      height: 100%;
    }
    .circle {
      height: 6px;
      position: relative;
      top: -30px;
      display: flex;
      justify-content: center;
      z-index: 3;
    }
    .circle ol {
      height: 100%;
      display: flex;
    }
    .circle ol li {
      width: 6px;
      height: 6px;
      margin: 0 4px;
      background-color: #fff;
      border-radius: 3px;
    }
    .circle .current {
      width: 16px;
    }
</style>
