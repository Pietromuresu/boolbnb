<script>


  // Import Swiper Vue.js components
  import { Swiper, SwiperSlide } from 'swiper/vue';

  // Import Swiper styles
  // import 'swiper/css';

    import 'swiper/css/pagination';
    import 'swiper/css/navigation';

  // import './style.css';

  // import required modules
  import { Pagination, Navigation } from 'swiper/modules';
import axios from 'axios';
import { store } from '../../store/store';

  export default {
    components: {
      Swiper,
      SwiperSlide,
    },
    props: {
      apartment: Object,
      gallery: Array,
      isAdmin: Boolean,
    },
    setup() {
      return {
        modules: [Pagination, Navigation],
      };
    },

    methods: {
      deleteImg(id){
        axios.get('sanctum/csrf-cookie')
        .then(() => {
          axios.delete(store.adminUrl + 'image/' + id)
            .then(result => {
              this.$emit('getApi');
            })
        })
      }
    }
  };
</script>
<template>
  <swiper
    :slidesPerView="1"
    :spaceBetween="30"
    :loop="true"
    :pagination="{
      clickable: true,
    }"
    :navigation="true"
    :modules="modules"
    class="mySwiper"
  >
    <swiper-slide>
      <img class="w-100" :src="apartment.img_path ? apartment.img_path : '/img/house-placeholder.png'" alt="">

    </swiper-slide>
    <swiper-slide v-for="photo in this.gallery" :key="photo.id">
      <img class="w-100" :src="photo.img_path ? photo.img_path : '/img/house-placeholder.png'" alt="">
      <div v-if="isAdmin" @click="deleteImg(photo.id)" class="delete-img">
        <i class="fa-solid fa-trash"></i>
      </div>
    </swiper-slide>
  </swiper>
</template>
<style lang="scss" scoped>
#app {
  height: 100%;
}
html,
body {
  position: relative;
  height: 100%;
}

body {
  background: #eee;
  font-family: Helvetica Neue, Helvetica, Arial, sans-serif;
  font-size: 14px;
  color: #000;
  margin: 0;
  padding: 0;
}

.swiper {
  width: 100%;
  height: 100%;
}

.swiper-slide {
  text-align: center;
  font-size: 18px;
  background: #fff;
  height: 600px;
  /* Center slide text vertically */
  display: flex;
  justify-content: center;
  align-items: center;
}

.swiper-slide img {
  display: block;
  width: 100%;
  height: 100%;
  object-fit: cover;
}

.swiper {
  margin-left: auto;
  margin-right: auto;
}

//media-query
@media screen and (max-width: 1200px) {
  .swiper-slide {

  height: 100%;

}
}

.delete-img{
  padding: 3px 8px;
  border-radius: 5px;
  position: absolute;
  top: 10px;
  right: 10px;

  &:hover{
    border: 1px solid rgb(254, 197, 197);
  }
}

</style>
