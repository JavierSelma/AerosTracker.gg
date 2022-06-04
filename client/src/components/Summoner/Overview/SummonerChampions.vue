<template>
  <div class="bg-gray-900 rounded-lg">
    <span class="mx-8 text-white text-lg font-bold uppercase">CHAMPIONS</span>
    <div v-if="stats.champion.length">
      <div class="flex items-baseline px-4 mt-3 text-xs bg-gray-900 font-semibold text-left uppercase">
        <div class="ml-2 text-base w-champion">Champion</div>
        <div class="w-plays">Games</div>
        <div class="w-winrate">Winrate</div>
        <div class="w-kda">KDA</div>
      </div>
      <ul class="mt-1 text-sm text-left text-gray-100">
        <li
          v-for="(champion, index) in stats.champion"
          :key="index"
          :class="[{'rounded-b-lg': index === stats.champion.length - 1}, {'bg-gray-900': index % 2 === 0}]"
          class="relative flex items-center px-4 py-2 leading-tight"
        >
          <div class="absolute text-xs" style="left: 6px;">{{ index + 1 }}.</div>
          <div class="flex items-center ml-2 w-champion">
            <div
              :style="{backgroundImage: `url('${champion.champion.icon}')`}"
              class="flex-shrink-0 w-8 h-8 bg-center bg-cover rounded-full bg-gray-900"
            ></div>
            <div class="mx-1 truncate">{{ champion.champion.name }}</div>
          </div>
          <div class="w-plays">
            <div class="text-xs">{{ champion.count }}</div>
          </div>
          <div class="w-winrate">
            <div class="text-xs">{{ champion.wins * 100 / champion.count|percent }}</div>
          </div>
          <div class="w-kda">
            <div
              class="text-xs"
            >{{ kda(champion.kills, champion.deaths, champion.assists) }}</div>
          </div>
        </li>
      </ul>
    </div>
    <div v-else class="px-4 py-2">
      <div>No champions have been found.</div>
      <div>😕</div>
    </div>
  </div>
</template>

<script>
import { mapState } from 'vuex'

export default {
  components: {
  },

  computed: {
    bestKda() {
      const bestChamp = this.stats.champion.reduce((a, b) => {
        return this.kda(a.kills, a.deaths, a.assists) > this.kda(b.kills, b.deaths, b.assists) ? a : b
      })
      return this.kda(bestChamp.kills, bestChamp.deaths, bestChamp.assists)
    },
    mostPlayed() {
      return this.stats.champion.reduce((a, b) => a.count > b.count ? a : b).count
    },
    ...mapState({
      stats: state => state.summoner.overview.stats
    }),
  },

  methods: {
    kda(kills, deaths, assists) {
      if (kills === 0 && deaths === 0 && assists === 0) {
        return 0
      }
      return this.$options.filters.round((kills + assists) / deaths)
    },
    widthBar(value, total) {
      return `${value * 36 / total}px`
    }
  }
}
</script>

<style scoped>
.w-champion {
  width: 110px;
}

.w-plays {
  width: 55px;
}

.w-winrate {
  width: 65px;
}

.w-kda {
  width: 36px;
}
</style>
