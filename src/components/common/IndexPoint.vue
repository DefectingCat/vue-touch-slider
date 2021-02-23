.<template>
  <div>
    <!-- 根据小圆点的数量来动态设定宽度 -->
    <ol ref="ol" @click="pointClick" :style="{ width: `${num * 10 * 2}px` }">
      <slot>
        <li></li>
      </slot>
    </ol>
  </div>
</template>

<script>
export default {
  props: {
    num: Number
  },
  methods: {
    pointClick(e) {
      if (e.target.nodeName.toLowerCase() == 'li') {
        this.$emit('pointClick', e);
      }
    }
  }
};
</script>

<style>
ol {
  display: flex;
  list-style: none;
  justify-content: space-around;
}
</style>
