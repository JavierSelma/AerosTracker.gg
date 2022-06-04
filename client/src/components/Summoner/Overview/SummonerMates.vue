<template>
  <div class="mt-4 bg-gray-900 rounded-lg">
    <div class="pb-2">
      <span class="mx-4 text-lg font-semibold uppercase">FRIENDS</span>
      <div v-if="hasMates" class="px-4 py-2 text-sm text-left">
        <div class="flex items-baseline text-xs font-semibold uppercase">
          <div class="w-2/4 text-base">Summoner</div>
          <div class="w-1/4">Record</div>
          <div class="w-1/4">Winrate</div>
        </div>
        <ul class="mt-1 text-gray-500">
          <li
            v-for="mate in mates.slice(0, maxMates)"
            :key="mate.name"
            class="flex items-center justify-between"
          >
            <router-link
              :to="{ name: 'summoner', params: { region: $route.params.region, name: mate.name }}"
              class="w-2/4 truncate hover:text-teal-200"
            >{{ mate.name }}</router-link>
            <div class="w-1/4">{{ mate.wins }} / {{ mate.losses }}</div>
            <div class="w-1/4"> {{ winrate(mate.wins, mate.count) }} % </div>
          </li>
        </ul>
      </div>
      <div v-else class="px-4 py-2 text-center">
        <div>No friends have been found.</div>
        <div>😕</div>
      </div>
    </div>
  </div>
</template>

<script>
import { mapState } from 'vuex'

export default {
  components: {
  },

  data() {
    return {
      maxMates: 15
    }
  },

  computed: {
    hasMates() {
      return this.mates.length > 0
    },
    ...mapState({
      mates: state => state.summoner.overview.stats.mates
    }),
  },

  methods: {
    getWinrateColor(wins, count, background = true) {
      const winrate = this.winrate(wins, count)
      if (winrate >= 70) {
        return background ? 'bg-yellow-400' : 'text-yellow-400'
      } else if (winrate >= 60) {
        return background ? 'bg-teal-500' : 'text-teal-500'
      } else if (winrate >= 50) {
        return background ? 'bg-teal-300' : 'text-teal-300'
      }
      return background ? 'bg-teal-200' : 'text-teal-200'
    },
    winrate(wins, count) {
      let winrate = wins * 100 / count
      return Math.trunc(winrate)
    }
  }
}
</script>
