.<template>
  <div>
    <ol ref="ol">
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
