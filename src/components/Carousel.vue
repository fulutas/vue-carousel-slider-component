<template>
  <div class="carousel">
    <slot :currentSlide="currentSlide" />

    <!-- Navigation -->
    <div v-if="navEnabled" class="navigate">
      <div class="toggle-page left">
        <i @click="prevSlide" class="fas fa-chevron-left"></i>
      </div>
      <div class="toggle-page right">
        <i @click="nextSlide" class="fas fa-chevron-right"></i>
      </div>
    </div>

    <!-- Pagination -->
    <div v-if="paginationEnabled" class="pagination">
        <span @click="goToSlide(index)" v-for="(slide, index) in getSlideCount" :key="index" :class="{ active : index +1 === currentSlide }">
        </span>
    </div>

  </div>
</template>

<script>
import { ref, onMounted } from "vue";

export default {

  props : ['startAutoPlay', 'timeout', 'navigation', 'pagination'],

  setup(props) {
    const currentSlide = ref(1); // mevcut slide index
    const getSlideCount = ref(null) // toplam slide sayısı
    const autoPlayEnabled = ref(props.startAutoPlay === undefined ? true : props.startAutoPlay) // otomatik oynatma 
    const timeoutDuration = ref(props.timeout === undefined ? 5000 : props.timeout) // geçiş süresi
    const paginationEnabled = ref(props.pagination === undefined ? true : props.pagination) // sayfalama
    const navEnabled = ref(props.navigation === undefined ? true : props.navigation) // ileri geri iconlar

    // next slide
    const nextSlide = () => {
        if(currentSlide.value === getSlideCount.value){
            currentSlide.value = 1
            return;
        } else {
            currentSlide.value += 1
        }
    }

    // prev slide
    const prevSlide = () => {
        if(currentSlide.value === 1){
            currentSlide.value = getSlideCount.value
        } else {
            currentSlide.value -= 1;
        }
    }

    // goToSlide
    const goToSlide = (index) => {
        // index 1 den başlar
        currentSlide.value = index +1
    }

    // autoplay
    const autoPlay = () => {
        setInterval(() => {
            nextSlide()
        }, timeoutDuration.value)
    }
    
    if(autoPlayEnabled.value){
        autoPlay();
    }

    onMounted(() => {
        getSlideCount.value = document.querySelectorAll('.slide').length;
    })

    return {
         currentSlide, 
         nextSlide,
         prevSlide,
         getSlideCount,
         goToSlide,
         paginationEnabled,
         navEnabled,

        };
  },
};
</script>

<style lang="scss">

.navigate{
    padding: 0 16px;
    height: 100%;
    width: 100%;
    position: absolute;
    display: flex;
    justify-content: center;
    align-items: center;

    .toggle-page{
        display: flex;
        flex: 1;
    }

    .right {
        justify-content: flex-end;
    }

    i {
        cursor: pointer;
        display: flex;
        align-items: center;
        justify-content: center;
        border-radius: 50%;
        width: 40px;
        height: 40px;
        background-color: #6347c7;
        color: #fff;
    }
}

.pagination{
    position: absolute;
    bottom:24px;
    width: 100%;
    gap: 16px;
    display: flex;
    justify-content: center;
    align-items: center;

    span {
        cursor: pointer;
        width: 20px;
        height: 20px;
        border-radius: 50%;
        background-color: #FFF;
        box-shadow: 0 1px 3px 0 rgba(0, 0, 0, 0.1), 0 1px 2px 0 rgba(0, 0, 0, 0.6);
    }

    .active{
        background-color: #6347c7;
    }
}

</style>
