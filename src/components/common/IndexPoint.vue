.<template>
  <div>
    <ol ref="ol" @click="btnClick">
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
  mounted() {
    // 单个圆点的宽度就不动态获取了，这里意思一下
    this.$refs.ol.style.width = `${this.num * 10 * 2}px`;
  },
  methods: {
    btnClick(e) {
      if (e.target.nodeName.toLowerCase() == 'li') {
        this.$emit('btnClick', e);
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
