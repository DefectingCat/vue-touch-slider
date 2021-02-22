.<template>
  <div @click="btnClick">
    <slot>
      <button>*</button>
    </slot>
  </div>
</template>

<script>
export default {
  methods: {
    btnClick(e) {
      this.$emit('btnClick', e);
    }
  }
};
</script>

<style scoped>
div {
  height: 35px;
  width: 35px;
  border: 0;
  background-color: rgba(201, 201, 201, 0.5);
  backdrop-filter: blur(5px);
  border-radius: 4px;
  display: flex;
  justify-content: center;
  align-items: center;
}
</style>
