<template>
  <div class="relative overflow-hidden bg-gray-900">
    <LazyBackground
      :image-source="require('@/assets/img/vi-warring-kingdoms.jpg')"
      image-class="absolute inset-0 bg-center"
      transition-name="fade"
    ></LazyBackground>

    <div class="flex flex-col items-center justify-center h-screen">
      <div class="absolute top-0 right-0">
        <div class="relative w-20 h-2 mt-4 mr-4 line line-top"></div>
      </div>

      <div class="absolute bottom-0 left-0">
        <div class="relative w-20 h-2 mb-4 ml-4 line line-bottom"></div>
      </div>

      <div class="relative flex flex-col items-center w-full max-w-lg">
        <img class="absolute logo" src="@/assets/img/logoat.png" alt="logo" />
        <SearchForm @formSubmit="redirect" :homepage="true" />
      </div>
    </div>
  </div>
</template>

<script>
import LazyBackground from '@/components/Common/LazyBackgroundImage.vue'
import SearchForm from '@/components/Form/SearchForm.vue'

export default {
  components: {
    LazyBackground,
    SearchForm
  },

  methods: {
    redirect(summoner, region) {
      this.$router.push(`/summoner/${region}/${summoner}`)
    },
  },

  metaInfo() {
    return {
      title: 'AerosTracker.gg',
    }
  }
}
</script>

<style scoped>

.logo {
  top: -250px;
  left: -10px;
}
</style>
