.<template>
  <div class="slider">
    <TouchSlider :images="images" :animeTime="'500ms'" />
  </div>
</template>

<script>
import TouchSlider from '@/components/common/TouchSlider';
export default {
  components: {
    TouchSlider
  },
  data() {
    return {
      images: [
        { id: 1, src: require('../../assets/img/1.webp') },
        { id: 2, src: require('../../assets/img/2.webp') },
        { id: 3, src: require('../../assets/img/3.webp') },
        { id: 4, src: require('../../assets/img/4.webp') },
        { id: 5, src: require('../../assets/img/5.webp') }
      ]
    };
  }
};
</script>

<style scoped></style>
