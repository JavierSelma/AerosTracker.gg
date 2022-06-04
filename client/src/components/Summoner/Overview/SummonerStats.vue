<template>
  <div v-if="stats.global" class="mt-4 bg-gray-900 rounded-lg">
    <div class="mt-4">
      <span class="mx-4 text-lg bg-gray-900 font-semibold uppercase">STATS</span>
    </div>
    <div class="flex items-center bg-gray-900 py-2 mt-2">
      <div
        v-for="(role, index) in stats.role"
        :key="index"
        class="flex flex-col items-center w-1/5"
      >
        <div class="mydiv"> {{ role.wins }}W - {{ role.losses }}L</div>
        <div
          :style="{backgroundImage: `url(${require('@/assets/img/roles/' + role.role + '.png')})`}"
          class="w-4 h-4 mt-1 bg-center bg-cover"
        ></div>
        <div class="text-xs text-blue-200">{{ role.count }}</div>
      </div>
    </div>
    <div class="py-2 text-sm text-center">
      <div class="flex items-baseline px-4 text-xs font-semibold text-red uppercase">
        <div class="w-1/4 text-base text-left">Stat</div>
        <div class="w-1/4">Total</div>
        <div class="w-1/4">Per min</div>
        <div class="w-1/4">Avg</div>
      </div>
      <ul class="mt-1 text-gray-500">
        <li
          v-for="(stat, name, index) in globalStatsKeys"
          :key="index"
          :class="{'bg-gray-760': index % 2 !== 0}"
          class="flex items-center justify-between px-4 py-1 leading-tight"
        >
          <div class="w-1/4 text-left capitalize">{{ name }}</div>
          <div class="w-1/4">{{ stat|kilo(false) }}</div>
          <div class="w-1/4">{{ stat / (stats.global.time / 60)|round }}</div>
          <div class="w-1/4">{{ stat / stats.global.count|round }}</div>
        </li>
        <li class="flex items-center justify-between px-4 py-1 leading-tight bg-gray-760">
          <div class="w-1/4 text-left whitespace-no-wrap">Time</div>
          <div class="w-1/4">{{ stats.global.time|secToHours }}</div>
          <div class="w-1/4"></div>
          <div class="w-1/4">{{ (stats.global.time / stats.global.count)|secToTime(true) }}</div>
        </li>
      </ul>
      <div class="flex items-baseline px-4 mt-3 text-xs font-semibold uppercase">
        <div class="w-5/12 text-base text-left">Class</div>
        <div class="w-3/12">Winrate</div>
        <div class="w-4/12">Record</div>
      </div>
      <ul class="mt-1 text-gray-500">
        <li
          v-for="(championClass, index) in stats.class"
          :key="index"
          :class="{'bg-gray-760': index % 2 !== 0}"
          class="flex items-center justify-between px-4 py-1 leading-tight"
        >
          <div class="w-5/12 text-left capitalize">{{ championClass.id }}</div>
          <div
            :class="calculateWinrate(championClass.wins, championClass.count).color"
            class="w-3/12"
          >{{ calculateWinrate(championClass.wins, championClass.count).winrate|percent }}</div>
          <div class="w-4/12">
            <span
              class="font-semibold text-white"
            >{{ championClass.wins }}</span>
            <span class="mx-1 font-semibold text-gray-400">-</span>
            <span
              class="font-semibold text-white"
            >{{ championClass.losses }}</span>
          </div>
        </li>
      </ul>
      <template v-if="leagueStatsByType('Ranked').length">
        <div class="flex items-baseline px-4 mt-3 text-xs font-semibold uppercase">
          <div class="w-5/12 text-base text-left">Ranked</div>
          <div class="w-3/12">Winrate</div>
          <div class="w-4/12">Record</div>
        </div>
        <ul class="mt-1 text-gray-500">
          <li
            v-for="(league, index) in leagueStatsByType('Ranked')"
            :key="index"
            :class="{'bg-gray-760': index % 2 !== 0}"
            class="flex items-center justify-between px-4 py-1 leading-tight"
          >
            <div class="w-5/12 text-left capitalize">{{ league.name.toLowerCase() }}</div>
            <div
              :class="calculateWinrate(league.wins, league.count).color"
              class="w-3/12"
            >{{ calculateWinrate(league.wins, league.count).winrate|percent }}</div>
            <div class="w-4/12">
              <span
                class="font-semibold text-white"
              >{{ league.wins }}</span>
              <span class="mx-1 font-semibold text-gray-400">-</span>
              <span
                class="font-semibold text-white"
              >{{ league.losses }}</span>
            </div>
          </li>
        </ul>
      </template>
      <template v-if="leagueStatsByType('Normal').length">
        <div class="flex items-baseline px-4 mt-3 text-xs font-semibold uppercase">
          <div class="w-5/12 text-base text-left">Normal</div>
          <div class="w-3/12">Winrate</div>
          <div class="w-4/12">Record</div>
        </div>
        <ul class="mt-1 text-gray-500">
          <li
            v-for="(league, index) in leagueStatsByType('Normal')"
            :key="index"
            :class="{'bg-gray-760': index % 2 !== 0}"
            class="flex items-center justify-between px-4 py-1 leading-tight"
          >
            <div class="w-5/12 text-left capitalize">{{ league.name }}</div>
            <div
              :class="calculateWinrate(league.wins, league.count).color"
              class="w-3/12"
            >{{ calculateWinrate(league.wins, league.count).winrate|percent }}</div>
            <div class="w-4/12">
              <span
                class="font-semibold text-white"
              >{{ league.wins }}</span>
              <span class="mx-1 font-semibold text-gray-400">-</span>
              <span
                class="font-semibold text-white"
              >{{ league.losses }}</span>
            </div>
          </li>
        </ul>
      </template>
    </div>

    <div class="flex flex-col items-center pb-2 leading-snug">
      <div
        class="text-xl text-white-400"
      >{{ calculateWinrate(stats.global.wins, stats.global.count).winrate|percent }}</div>
      <span class="text-xs">Global winrate</span>
    </div>
  </div>
</template>

<script>
import { mapState } from 'vuex'
import { gameModes } from '@/data/data.js'

export default {
  components: {
  },

  computed: {
    mostPlayedRole() {
      return Math.max(...this.stats.role.map(r => r.count), 0)
    },
    globalStatsKeys() {
      // eslint-disable-next-line no-unused-vars
      const { id, wins, losses, count, time, kp, ...rest } = this.stats.global
      return rest
    },
    ...mapState({
      stats: state => state.summoner.overview.stats
    }),
  },

  methods: {
    calculateWinrate(wins, count) {
      const winrate = count !== 0 ? wins / count * 100 : 0
      const color = winrate >= 50 ? 'text-green-500' : 'text-red-700'
      return {
        winrate,
        color
      }
    },
    leagueStatsByType(typeName) {
      return this.stats.league
        .map(l => {
          return { ...l, ...gameModes[l.id] }
        })
        .filter(l => l.type === typeName)
    },
    roundedRoleLosses(win, count) {
      return win === count ? 'rounded-full' : 'rounded-b-full'
    },
    roundedRoleWins(win, count) {
      return win === count ? 'rounded-full' : 'rounded-t-full'
    },
    winLossColor(win, loss) {
      const colors = {
        win: 'text-gray-200',
        loss: 'text-gray-200'
      }
      win >= loss ? colors.win = 'text-green-400' : colors.loss = 'text-red-400'
      return colors
    }
  }
}
</script>