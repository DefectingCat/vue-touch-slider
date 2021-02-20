.<template>
  <!-- 根据计算的单个图片宽度，动态的绑定外层 div 宽度 -->
  <div class="slider" :style="{ width: imgWidth + 'px' }">
    <!-- 克隆了一张图片后，动态的向左偏移负的单张图片的宽度 -->
    <ul
      ref="ul"
      class="wrapper"
      :style="{
        width: imgWidth * images.length + 'px',
        transform: `translateX(${-imgWidth}px)`
      }"
      @touchstart="touchStart"
      @touchmove="touchMove"
      @touchend="touchEnd"
      @transitionend="transEnd"
    >
      <!-- 克隆最后一张图片 -->
      <li>
        <img :src="images[images.length - 1].src" alt="" class="roll-img" />
      </li>
      <li v-for="item in images" :key="'img' + item.id">
        <img :src="item.src" alt="" class="roll-img" ref="img" />
      </li>
      <!-- 克隆第一张图片 -->
      <li>
        <img :src="images[0].src" alt="" class="roll-img" />
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  name: 'TouchSlider',
  components: {},
  props: {
    // 图片队列
    images: Array,
    // 动画时间
    animeTime: String
  },
  data() {
    return {
      // 单张图片的宽度
      imgWidth: 0,
      // 点击时的坐标
      startX: 0,
      // 移动的距离
      moveX: 0,
      // 图片索引，用于控制位置
      imgIndex: 0,
      // 等待过渡完成再结束触摸响应，防止在一定时间内过渡滑动
      flag: Date.now(),
      // 延时序列号
      timer: 0
    };
  },
  computed: {
    // 选中 ul DOM
    imgBoxStyle() {
      return this.$refs.ul.style;
    },
    // 图片移动的距离，等于当前图片宽度 加上 图片宽度乘以图片序列，结果为负数
    translateX() {
      return -(this.imgWidth + this.imgWidth * this.imgIndex);
    }
  },
  mounted() {
    // 计算单个图片的宽度，做移动端适配
    this.imgWidth = this.$refs.img[0].offsetWidth;
    this.autoPlay();
  },
  methods: {
    move() {
      // 移动方法，添加过渡动画，根据图片序列移动图片
      this.imgBoxStyle.transition = `all ${this.animeTime}`;
      this.imgBoxStyle.transform = `translateX(${this.translateX}px)`;
    },
    touchStart(e) {
      // 触摸开始
      if (Date.now() - this.flag > 300) {
        clearInterval(this.timer);
        // 获取点击时的 X 坐标
        this.startX = e.touches[0].clientX;
      }
    },
    touchMove(e) {
      // 防止在一定时间内过渡滑动
      if (Date.now() - this.flag > 300) {
        // 移动时的坐标减去点击时的坐标等于移动的距离
        this.moveX = e.touches[0].clientX - this.startX;
        // 移动图片
        this.imgBoxStyle.transition = `none`;
        this.imgBoxStyle.transform = `translateX(${this.translateX +
          this.moveX}px)`;
      }
    },
    touchEnd() {
      // 当触摸大于 70 像素，触发移动动画，移动完整图片
      if (this.moveX > 70) {
        this.imgIndex--;
        this.move();
        // 当触摸小于 -70 像素，触发移动动画，移动完整图片
      } else if (this.moveX < -70) {
        this.imgIndex++;
        this.move();
        // 当在二者之间时，图片归为
      } else {
        this.move();
      }
      this.startX = 0;
      this.moveX = 0;
      this.flag = Date.now();
      this.autoPlay();
    },
    transEnd() {
      if (this.imgIndex == -1) {
        // 如果当前序列等于 -1，也就是克隆的图片，则偷偷调整图片队列
        this.imgIndex = this.images.length - 1;
        this.imgBoxStyle.transition = `none`;
        this.imgBoxStyle.transform = `translateX(${this.translateX}px)`;
      } else if (this.imgIndex == this.images.length) {
        // 反之亦然
        this.imgIndex = 0;
        this.imgBoxStyle.transition = `none`;
        this.imgBoxStyle.transform = `translateX(${this.translateX}px)`;
      }
    },
    autoPlay() {
      this.timer = setInterval(() => {
        this.imgIndex++;
        this.move();
      }, 3000);
    }
  }
};
</script>

<style scoped>
.roll-img {
  width: 600px;
}

.slider {
  overflow: hidden;
}
.wrapper {
  display: flex;
}

@media only screen and (max-width: 376px) {
  .roll-img {
    width: 375px;
  }
}
</style>
