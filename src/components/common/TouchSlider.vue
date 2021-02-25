.<template>
  <!-- 根据计算的单个图片宽度，动态的绑定外层 div 宽度 -->
  <div class="slider" :style="{ width: imgWidth + 'px' }">
    <ImgBtn class="btn btn-left" @click.native="previous" v-if="hasBtn">
      <template>
        <svg class="icon" aria-hidden="true">
          <use xlink:href="#icon-zuo"></use>
        </svg>
      </template>
    </ImgBtn>

    <!-- 克隆了一张图片后，动态的向左偏移负的单张图片的宽度 -->
    <ul
      class="wrapper"
      :style="{
        width: imgWidth * images.length + 'px',
        transform: `translateX(${translateX}px)`,
        transition: transitionX
      }"
      @touchstart="touchStart"
      @touchmove="touchMove"
      @touchend="touchEnd"
      @transitionend="transEnd"
      @mouseover="pausePlay"
      @mouseout="autoPlay"
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

    <IndexPoint
      class="index-point"
      :num="images.length"
      @pointClick="pointClick"
    >
      <template>
        <li
          v-for="(item, index) in images"
          :key="'point' + item.id"
          :class="{ active: index == pointIndex }"
          :id="index"
        ></li>
      </template>
    </IndexPoint>

    <ImgBtn class="btn btn-right" @click.native="next" v-if="hasBtn">
      <template>
        <svg class="icon" aria-hidden="true">
          <use xlink:href="#icon-you"></use>
        </svg>
      </template>
    </ImgBtn>
  </div>
</template>

<script>
import ImgBtn from '@/components/common/ImgBtn';
import IndexPoint from '@/components/common/IndexPoint';

export default {
  name: 'TouchSlider',
  components: {
    ImgBtn,
    IndexPoint
  },
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
      // 图片索引，用于控制位置
      imgIndex: 0,
      // 点击时的坐标
      startX: 0,
      // 移动的距离
      moveX: 0,
      // 等待过渡完成再结束触摸响应，防止在一定时间内过渡滑动
      flag: Date.now(),
      // 延时
      timer: 0,
      // 移动位置
      tlatex: '',
      // 过渡时间
      tsionx: '',
      // 保存触摸前图片的位置
      lastX: 0,
      // 控制按钮显示
      hasBtn: true
    };
  },
  computed: {
    // 选中 ul DOM
    imgBoxStyle() {
      return this.$refs.ul.style;
    },
    // 图片移动的距离，等于当前图片宽度 加上 图片宽度乘以图片序列，结果为负数
    translateX: {
      set(val) {
        this.tlatex = val;
      },
      get() {
        return this.tlatex;
      }
    },
    pointIndex() {
      // 图片索引在动画时会越界，使用计算数据防止小圆点越界
      if (this.imgIndex == -1) {
        return this.images.length - 1;
      } else if (this.imgIndex == this.images.length) {
        return 0;
      } else {
        return this.imgIndex;
      }
    },
    transitionX: {
      set(val) {
        this.tsionx = val;
      },
      get() {
        return this.tsionx;
      }
    }
  },
  mounted() {
    // 计算单个图片的宽度，做移动端适配
    this.imgWidth = this.$refs.img[0].offsetWidth;
    this.move(false);
    this.autoPlay();
    if (this.imgWidth < 400) {
      this.hasBtn = false;
    }
  },
  methods: {
    move(anime) {
      // 移动方法，添加过渡动画，根据图片序列移动图片
      if (anime) {
        this.transitionX = `all ${this.animeTime}ms`;
      } else {
        this.transitionX = `none`;
      }
      this.translateX = -(this.imgWidth + this.imgWidth * this.imgIndex);
    },
    touchStart(e) {
      this.pausePlay();
      // 触摸开始
      if (Date.now() - this.flag > Number(this.animeTime) + 10) {
        // 获取点击时的 X 坐标
        this.startX = e.touches[0].clientX;
        // 点击开始时保存当前图片的位置
        this.lastX = this.translateX;
      }
    },
    touchMove(e) {
      // 防止在一定时间内过渡滑动
      if (Date.now() - this.flag > Number(this.animeTime) + 10) {
        // 移动时的坐标减去点击时的坐标等于移动的距离
        this.moveX = e.touches[0].clientX - this.startX;
        // 移动图片
        this.transitionX = `none`;
        // 滑动位置等于上次的位置加上手指移动的距离
        this.translateX = this.lastX + this.moveX;
      }
    },
    touchEnd() {
      if (Date.now() - this.flag > Number(this.animeTime) + 10) {
        // 当触摸大于 70 像素，触发移动动画，移动完整图片
        if (this.moveX > 70) {
          this.imgIndex--;
          this.move(true);
          // 当触摸小于 -70 像素，触发移动动画，移动完整图片
        } else if (this.moveX < -70) {
          this.imgIndex++;
          this.move(true);
          // 当在二者之间时，图片归位
        } else {
          this.move(true);
        }
        this.startX = 0;
        this.moveX = 0;
        this.flag = Date.now();
      }
      this.autoPlay();
    },
    transEnd() {
      if (this.imgIndex == -1) {
        // 如果当前序列等于 -1，也就是克隆的图片，则偷偷调整图片队列
        this.imgIndex = this.images.length - 1;
        this.move(false);
      } else if (this.imgIndex == this.images.length) {
        // 反之亦然
        this.imgIndex = 0;
        this.move(false);
      }
    },
    autoPlay() {
      this.timer = setInterval(() => {
        // 当页面处于后台时，transEnd会失效
        if (document.visibilityState != 'hidden') {
          this.imgIndex++;
          this.move(true);
        }
        console.log(document.visibilityState);
      }, 3000);
    },
    pointClick(e) {
      this.imgIndex = e.target.id;
      this.move(true);
    },
    previous() {
      if (Date.now() - this.flag > Number(this.animeTime) + 10) {
        this.imgIndex--;
        this.move(true);
        this.flag = Date.now();
      }
    },
    next() {
      if (Date.now() - this.flag > Number(this.animeTime) + 10) {
        this.imgIndex++;
        this.move(true);
        this.flag = Date.now();
      }
    },
    pausePlay() {
      clearInterval(this.timer);
    }
  }
};
</script>

<style scoped>
.roll-img {
  width: 600px;
}

.slider {
  position: relative;
  overflow: hidden;
}
.wrapper {
  display: flex;
}

.btn {
  position: absolute;
  top: 50%;
  transform: translateY(-50%);
  z-index: 1;
}
.btn-right {
  right: 0;
}

.index-point {
  position: absolute;
  bottom: 10%;
  left: 50%;
  transform: translateX(-50%);
}
.index-point li {
  height: 10px;
  width: 10px;
  background-color: rgba(255, 255, 255, 0.5);
  transition: all 0.3s;
  border-radius: 10px;
}
.index-point li.active {
  width: 20px;
}

@media only screen and (max-width: 376px) {
  .roll-img {
    width: 375px;
  }
}
</style>
