<template>
  <div class="flex flex-col min-h-screen overflow-hidden bg-blue-900">
    <LazyBackground
      :image-source="require('@/assets/img/vi-warring-kingdoms.jpg')"
      image-class="absolute z-0 w-full h-200"
      more-backgrounds="linear-gradient(180deg, rgba(42, 67, 101, 0) 0%, #2A4365 50%),"
      transition-name="fade"
    ></LazyBackground>

    <header
      :class="bgHeader ? 'header-scrolled' : 'bg-transparent'"
      class="fixed left-0 right-0 z-20 px-4 text-teal-100 transition-colors duration-100 ease-in-out border-b-2 header"
      style="border-color: rgba(144, 205, 244, 0.4);"
    >
      <div class="flex items-center justify-between py-2 -mb-2px">
        <div class="flex flex-1">
          <router-link to="/">
            <img class="block h-10" src="@/assets/img/logoat.png" alt="AerosTracker.gg logo" />
          </router-link>
        </div>

        <SearchForm @formSubmit="redirect" :homepage="false" />

        <div class="flex-1">
          <div class="flex items-center justify-end">
          </div>
        </div>
      </div>
    </header>

    <div class="relative z-10 flex-grow mx-auto mt-20 text-white page-wrapper">
      <template v-if="summonerLoading || summonerFound">
        <template v-if="summonerLoading">
          <HeaderLoader />
        </template>
        <template v-else-if="summonerFound">
          <div class="flex items-center justify-between">
            <div>
              <div class="flex items-center mt-2">
                <Tooltip>
                  <template #trigger>
                    <h1 class="text-4xl font-extrabold">
                      {{ basic.account.name }}
                    </h1>
                  </template>
                  <template #default>
                    <div
                      v-if="basic.account.names.length > 1"
                      class="px-2 text-sm text-center text-white select-none"
                    >
                      <div>Old summoner names</div>
                      <ul class="pl-2 text-left list-disc list-inside">
                        <li
                          v-for="name in basic.account.names.slice(0, -1)"
                          :key="name.date"
                          class="text-teal-400"
                        >{{ name.name }}</li>
                      </ul>
                    </div>
                  </template>
                </Tooltip>
                <div
                  v-if="playing"
                  class="flex items-center px-3 py-1 mt-2 ml-4 bg-teal-800 border border-teal-400 rounded-full"
                >
                  <div class="w-2 h-2 rounded-full playing-dot bg-teal-flashy"></div>
                  <span class="ml-2 text-sm font-semibold text-teal-flashy">In Game</span>
                </div>
                <div
                  v-if="false"
                  class="inline-flex items-center px-2 py-1 mt-2 ml-4 leading-tight border border-teal-500 rounded"
                  style="background: rgba(40, 94, 97, 0.35);"
                >
                  <svg class="w-4 h-4 text-teal-600">
                    <use xlink:href="#star" />
                  </svg>
                  <div class="ml-1 text-xs font-bold text-teal-200">Favorite</div>
                </div>
              </div>
              <div class="flex mt-2">
                <div :class="{'playing': playing}" class="relative w-24 h-24">
                  <div
                    :class="{'border-2': !playing}"
                    class="relative z-10 w-24 h-24 bg-center bg-cover border-black rounded-full bg-gray-900"
                    :style="{backgroundImage: `url('https://raw.communitydragon.org/latest/plugins/rcp-be-lol-game-data/global/default/v1/profile-icons/${basic.account.profileIconId}.jpg')`}"
                  >
                    <div
                      class="absolute bottom-0 left-0 flex items-center justify-center w-8 h-8 text-xs font-extrabold text-white bg-gray-900 border-2 border-black rounded-full"
                    >{{ basic.account.summonerLevel }}</div>
                  </div>
                </div>

                <SummonerRanked
                  v-if="Object.entries(basic.ranked).length !== 0"
                  :ranked="basic.ranked"
                />
              </div>
            </div>
          </div>
          <div class="flex items-center justify-between">
            <!-- NAVIGATION -->
            <div class="pb-2">
              <router-link
                :to="{ name: 'summoner', params: { region: $route.params.region, name: $route.params.name }}"
                :class="isRouteActive('summoner')"
                class="pb-2 text-blue-300 border-b-2 border-transparent cursor-pointer hover:text-blue-100"
                exact
              >Overview</router-link>
            </div>

            <!-- Select Season -->
            <template v-if="$route.meta.season">
              <FilterSeason />
            </template>
          </div>
        </template>

        <!-- View -->
        <transition :name="tabTransition">
          <slot></slot>
        </transition>
      </template>

      <template v-else-if="summonerNotFound">
        <div class="flex justify-center mt-16">
          <div class="px-4 py-3 text-lg font-bold text-center text-blue-100 rounded-lg bg-gradient">
            <div>Player can't be found.</div>
            <div>😕</div>
          </div>
        </div>
      </template>
    </div>

    <MainFooter />
  </div>
</template>

<script>
import { mapState, mapActions, mapGetters } from 'vuex'
import FilterSeason from '@/components/Summoner/FilterSeason.vue'
import LazyBackground from '@/components/Common/LazyBackgroundImage.vue'
import MainFooter from '@/components/Layout/MainFooter.vue'
import SearchForm from '@/components/Form/SearchForm.vue'
import HeaderLoader from '@/components/Summoner/HeaderLoader.vue'
import SummonerRanked from '@/components/Summoner/SummonerRanked.vue'
import Tooltip from '@/components/Common/Tooltip.vue'

export default {
  components: {
    FilterSeason,
    LazyBackground,
    MainFooter,
    SearchForm,
    HeaderLoader,
    SummonerRanked,
    Tooltip
  },

  data() {
    return {
      bgHeader: false
    }
  },

  computed: {
    tabTransition() {
      return this.summonerFound && this.overviewLoaded ? 'tab' : 'none'
    },
    ...mapState({
      basic: state => state.summoner.basic
    }),
    ...mapGetters('summoner', ['playing', 'overviewLoaded', 'summonerFound', 'summonerNotFound', 'summonerLoading'])
  },

  watch: {
    $route(to, from) {
      if (from.params.region === to.params.region && from.params.name === to.params.name)
        return
      this.apiCall()
    }
  },

  created() {
    this.apiCall()
    window.addEventListener('scroll', this.handleScroll)
  },

  destroyed() {
    window.removeEventListener('scroll', this.handleScroll)
  },

  methods: {
    apiCall() {
      this.updateSettings({ name: 'region', value: this.$route.params.region.toLowerCase() })
      this.basicRequest({ summoner: this.$route.params.name, region: this.$route.params.region })
    },
    handleScroll() {
      this.bgHeader = window.scrollY > 25
    },
    isRouteActive(currentRoute) {
      return {
        'router-link-active': this.$route.name === currentRoute
      }
    },
    redirect(summoner, region) {
      this.$router.push(`/summoner/${region}/${summoner}`).catch(() => { })
    },
    ...mapActions('settings', ['updateSettings']),
    ...mapActions('summoner', ['basicRequest']),
  },

  metaInfo() {
    return {
      titleTemplate: this.summonerFound ? `${this.basic.account.name} | AerosTracker.gg %s` : 'AerosTracker.gg %s',
    }
  }
}
</script>

<style scoped>
.header-scrolled {
  background-color: rgba(0, 0, 0, 0.65);
}

.discord svg {
  width: 22px;
  height: 22px;
  transform-origin: bottom left;
  transition: 0.2s ease-in-out;
}

.discord:hover svg {
  width: 24px;
  height: 24px;
  transform: rotate(-5deg);
}

.discord:hover span {
  color: #ebf8ff;
}

.router-link-active {
  color: #fff;
  border-color: #fff;
}
.playing::before {
  z-index: 0;
  background: rgba(137, 160, 181, 0.2);
}

.playing::before,
.playing::after {
  content: "";
  position: absolute;
  height: 100px;
  width: 100px;
  top: -2px;
  left: -2px;
  right: 0;
  bottom: 0;
  border-radius: 50%;
}

.playing::after {
  z-index: 0;
  background: linear-gradient(
    to top,
    rgba(0, 0, 0, 0) 30%,
    rgb(36, 232, 204) 100%
  );
  animation: 0.75s linear 0s infinite normal none running rotate;
}

@keyframes rotate {
  100% {
    transform: rotate(360deg);
  }
}

.playing-dot {
  box-shadow: 0 0 0 0 rgba(0, 0, 0, 1);
  transform: scale(1);
  animation: 2.5s ease-in-out 0s infinite normal none running pulse;
}

@keyframes pulse {
  0% {
    transform: scale(0.95);
    box-shadow: 0 0 0 0 rgba(36, 232, 204, 0.7);
  }

  70% {
    transform: scale(1);
    box-shadow: 0 0 0 8px rgba(0, 0, 0, 0);
  }

  100% {
    transform: scale(0.95);
    box-shadow: 0 0 0 0 rgba(0, 0, 0, 0);
  }
}
</style>
