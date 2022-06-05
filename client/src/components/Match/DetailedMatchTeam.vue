<template>
  <table
    class="w-full table-fixed"
  >
    <thead class="leading-none heading-detailed">
      <tr
        :style="getHeadingColor(data.result)"
        class="relative font-semibold text-white heading-row"
      >
        <th class="py-5 w-players">
          <div class="flex justify-between">
            <span
              :class="allyTeam ? 'text-white' : 'text-white'"
              class="pl-2"
            >{{ allyTeam ? "Ally" : "Enemy" }} Team</span>
            <div
              v-if="data.result === 'Win'"
              :class="allyTeam ? 'text-white' : 'text-white'"
              class="flex pr-2"
            >
              <span class="ml-2px">VICTORY</span>
            </div>
          </div>
        </th>
        <th class="px-2 py-5 text-sm font-medium w-kda">K</th>
        <th class="px-2 py-5 text-sm font-medium w-kda">D</th>
        <th class="px-2 py-5 text-sm font-medium w-kda">A</th>
        <th class="px-2 py-5 text-sm font-medium w-minions">
          {{ statsFormat === "stats" ? "Cs" : "Cs/m" }}
        </th>
        <th class="px-2 py-5 text-sm font-medium w-vision">
          {{ statsFormat === "stats" ? "Vs" : "Vs/m" }}
        </th>
        <th class="px-2 py-5 text-sm font-medium w-gold-dmg-kp">Gold</th>
        <th class="px-2 py-5 text-sm font-medium w-gold-dmg-kp">
          Dmg
          <br />champ
        </th>
        <th class="px-2 py-5 text-sm font-medium w-gold-dmg-kp">
          Dmg
          <br />obj
        </th>
        <th class="px-2 py-5 text-sm font-medium w-gold-dmg-kp">
          Dmg
          <br />taken
        </th>
        <th class="px-2 py-5 text-sm font-medium w-gold-dmg-kp">KP</th>
      </tr>
    </thead>
    <tbody
      :class="{ 'border-b border-black': allyTeam }"
      class="leading-none"
    >
      <tr v-for="(player, index) in data.players" :key="player.name + index">
        <td class="py-2 border-r border-black">
          <div class="flex justify-between px-1">
            <div class="flex">
              <div class="flex items-center">
                <div
                  v-if="player.role !== 'NONE'"
                  :style="{
                    backgroundImage: `url(${require('@/assets/img/roles/' +
                      player.role +
                      '.png')})`,
                  }"
                  class="w-4 h-4 bg-center bg-cover"
                ></div>
              </div>
              <div
                :style="{ backgroundImage: `url('${player.champion.icon}')` }"
                class="relative w-8 h-8 ml-2 bg-center bg-cover rounded-full bg-gray-900"
              >
                <div
                  :class="
                    allyTeam
                      ? 'bg-gray-700 text-white'
                      : 'bg-gray-700 text-white'
                  "
                  class="absolute bottom-0 flex items-center justify-center w-4 h-4 rounded-full level-position text-xxs"
                >
                  <span>{{ player.level }}</span>
                </div>
              </div>
              <div class="flex flex-col justify-around ml-1">
                <Tooltip>
                  <template #trigger>
                    <div
                      :style="{
                        backgroundImage: `url(${
                          player.summonerSpell1 ? player.summonerSpell1.icon : null
                        })`,
                      }"
                      :class="{ 'cursor-pointer': player.summonerSpell1 }"
                      class="w-4 h-4 bg-center bg-cover rounded-md bg-gray-900"
                    ></div>
                  </template>
                  <template v-if="player.summonerSpell1" #default>
                    <div
                      class="flex max-w-sm p-2 text-xs text-left text-white select-none"
                    >
                      <div
                        :style="{
                          backgroundImage: `url('${player.summonerSpell1.icon}')`,
                        }"
                        class="flex-shrink-0 w-12 h-12 ml-1 bg-center bg-cover rounded-md bg-gray-900"
                      ></div>
                      <div class="ml-2 leading-tight">
                        <div class="text-base leading-none">
                          {{ player.summonerSpell1.name }}
                        </div>
                        <div class="mt-1 font-light text-white">
                          {{ player.summonerSpell1.description }}
                        </div>
                      </div>
                    </div>
                  </template>
                </Tooltip>
                <Tooltip>
                  <template #trigger>
                    <div
                      :style="{
                        backgroundImage: `url(${
                          player.summonerSpell2 ? player.summonerSpell2.icon : null
                        })`,
                      }"
                      :class="{ 'cursor-pointer': player.summonerSpell2 }"
                      class="w-4 h-4 bg-center bg-cover rounded-md bg-gray-900"
                    ></div>
                  </template>
                  <template v-if="player.summonerSpell2" #default>
                    <div
                      class="flex max-w-sm p-2 text-xs text-left text-white select-none"
                    >
                      <div
                        :style="{
                          backgroundImage: `url('${player.summonerSpell2.icon}')`,
                        }"
                        class="flex-shrink-0 w-12 h-12 ml-1 bg-center bg-cover rounded-md bg-gray-900"
                      ></div>
                      <div class="ml-2 leading-tight">
                        <div class="text-base leading-none">
                          {{ player.summonerSpell2.name }}
                        </div>
                        <div class="mt-1 font-light text-white">
                          {{ player.summonerSpell2.description }}
                        </div>
                      </div>
                    </div>
                  </template>
                </Tooltip>
              </div>
              <Tooltip>
                <template #trigger>
                  <div
                    @click="selectRunes(player)"
                    :class="{ 'cursor-pointer': player.perks }"
                    class="flex flex-col justify-around cursor-pointer ml-2px"
                  >
                    <div
                      :style="[
                        player.primaryRune
                          ? {
                            background: `url(${player.primaryRune}) center/cover`,
                          }
                          : '',
                      ]"
                      class="w-4 h-4 rounded-md bg-gray-900"
                    ></div>
                    <div
                      :style="[
                        player.secondaryRune
                          ? {
                            background: `url(${player.secondaryRune}) center/cover`,
                          }
                          : '',
                      ]"
                      class="w-4 h-4 rounded-md bg-gray-900"
                    ></div>
                  </div>
                </template>
                <template v-if="player.perks" #default>
                  <div
                    class="px-2 text-sm leading-relaxed text-center text-white select-none"
                  >
                    <p>Click to display</p>
                  </div>
                </template>
              </Tooltip>
              <div
                class="flex flex-col items-start justify-center ml-1 leading-none"
              >
                <router-link
                  v-if="player.summonerSpell1"
                  :to="{
                    name: 'summoner',
                    params: { region: $route.params.region, name: player.name },
                  }"
                  :class="{
                    'font-semibold text-yellow-400':
                      account.id === player.summonerId,
                  }"
                  class="overflow-hidden text-xs text-left text-gray-500 whitespace-no-wrap w-22 text-overflow hover:text-white"
                >{{ player.name }}</router-link>
                <div
                  v-else
                  class="overflow-hidden text-xs text-left text-gray-500 whitespace-no-wrap w-22 text-overflow"
                >
                  {{ player.name }}
                </div>
                <div class="text-gray-400 text-xxs">
                  {{ player.champion.name }}
                </div>
              </div>
            </div>
            <div class="flex items-center">
              <div v-if="player.rank">
                <svg class="w-5 h-5 ml-auto">
                  <use
                    :xlink:href="`#rank-${player.rank.tier.toLowerCase()}`"
                  />
                </svg>
                <div class="text-gray-600 text-xxs">
                  {{ player.rank.shortName }}
                </div>
              </div>
              <div v-else-if="!ranksLoaded">
                <DotsLoader width="30px" dot-width="10px" />
              </div>
              <div v-else class="w-5 h-5">
                <div class="-mt-1 text-2xl text-white">-</div>
              </div>
              <MatchItems :items="player.items" :one-row="true" />
            </div>
          </div>
        </td>
        <td class="relative">
          <div
            class="absolute inset-0 flex items-center justify-center p-2 text-sm text-white"
          >
            {{ player.stats.kills }}
          </div>
        </td>
        <td class="relative">
          <div
            class="absolute inset-0 flex items-center justify-center p-2 text-sm text-white"
          >
            {{ player.stats.deaths }}
          </div>
        </td>
        <td class="relative">
          <div
            class="absolute inset-0 flex items-center justify-center p-2 text-sm text-white"
          >
            {{ player.stats.assists }}
          </div>
        </td>
        <td class="relative">
          <div
            class="absolute inset-0 flex items-center justify-center p-2 text-sm text-white"
          >
            {{ player[statsFormat].minions }}
          </div>
        </td>
        <td class="relative">
          <div
            class="absolute inset-0 flex items-center justify-center p-2 text-sm text-white"
          >
            {{ player[statsFormat].vision }}
          </div>
        </td>
        <td class="relative">
          <div
            class="absolute inset-0 flex items-center justify-center p-2 text-sm text-white"
          >
            {{ roundStats(player[statsFormat].gold) }}
          </div>
        </td>
        <td class="relative">
          <div
            class="absolute inset-0 flex items-center justify-center p-2 text-sm text-white"
          >
            {{ roundStats(player[statsFormat].dmgChamp) }}
          </div>
        </td>
        <td class="relative">
          <div
            class="absolute inset-0 flex items-center justify-center p-2 text-sm text-white"
          >
            {{ roundStats(player[statsFormat].dmgObj) }}
          </div>
        </td>
        <td class="relative">
          <div
            class="absolute inset-0 flex items-center justify-center p-2 text-sm text-white"
          >
            {{ roundStats(player[statsFormat].dmgTaken) }}
          </div>
        </td>
        <td class="relative">
          <div
            class="absolute inset-0 flex items-center justify-center p-2 text-sm text-white"
          >
            {{ player.stats.kp }}
          </div>
        </td>
      </tr>
    </tbody>
  </table>
</template>

<script>
import { mapActions, mapState } from 'vuex'
import DotsLoader from '@/components/Common/DotsLoader.vue'
import Tooltip from '@/components/Common/Tooltip.vue'
import MatchItems from '@/components/Match/MatchItems.vue'

export default {
  components: {
    DotsLoader,
    Tooltip,
    MatchItems,
  },

  props: {
    allPlayers: {
      type: Array,
      required: true,
    },
    allyTeam: {
      type: Boolean,
      required: true,
    },
    data: {
      type: Object,
      required: true,
    },
    ranksLoaded: {
      type: Boolean,
      default: false
    }
  },

  computed: {
    statsFormat() {
      return this.percentSettings ? 'percentStats' : 'stats'
    },
    ...mapState({
      account: (state) => state.summoner.basic.account,
      percentSettings: (state) => state.settings.percent,
    }),
  },

  methods: {
    bgColor(player, stats) {
      const value = parseFloat(player.stats[stats])
      const biggestValue = Math.max(
        ...this.allPlayers.map((p) => parseFloat(p.stats[stats])),
        0
      )
      const opacity = (value / biggestValue).toFixed(2)
      const biggestValueStyle = {}
      if (value === biggestValue && value !== 0) {
        biggestValueStyle.boxShadow = 'rgba(181, 160, 122, 0.5) 0px 0px 10px'
        biggestValueStyle.border = '2px solid'
        biggestValueStyle.borderImageSlice = '1'
        biggestValueStyle.borderImageSource =
          'linear-gradient(to top, #edb457, #f9e9ce)'
        biggestValueStyle.borderCollapse = 'separate'
      }

      return {
        opacity
      }
    },
    displayBorderbottom(index) {
      return this.allyTeam || index !== this.data.players.length - 1
    },
    getHeadingColor(result) {
      switch (result) {
        case 'Win2':
          return {
            '--bg-img':
              'linear-gradient(90deg, rgba(1, 97, 28, 0.3) 0%, rgba(44, 82, 130, 0) 200% )',
          }
        case 'Fail2':
          return {
            '--bg-img':
              'linear-gradient(90deg, rgba(140, 0, 0, 0.3) 0%, rgba(44, 82, 130, 0) 200% )',
          }
      }
    },
    roundStats(value) {
      return this.percentSettings ? value : this.$options.filters.kilo(value)
    },
    selectRunes(player) {
      if (!player.perks) {
        return
      }

      this.displayOrHideRunes(player.perks)
    },
    ...mapActions('cdragon', ['displayOrHideRunes']),
  },
}
</script>

<style scoped>
.heading-row th {
  position: relative;
  z-index: 20;
}

.heading-row th:first-child:before {
  content: "";
  position: absolute;
  z-index: -10;
  top: 0;
  left: 0;
  height: 67px;
  width: 884px;
  background-image: var(--bg-img),
    linear-gradient(#2a4365 0%, #2b4c77 55%, #235a93 100%);
}

.heading-row th:first-child:after {
  content: "";
  position: absolute;
  right: -1px;
  top: 0;
  width: 1px;
  background-color: #2b6cb0;
  height: 67px;
}

.heading-detailed {
  box-shadow: inset 0 -1px #2b6cb0;
}

.level-position {
  left: -5px;
}

.w-players {
  width: 392px;
}

.w-kda {
  width: 36px;
}

.w-minions {
  width: 45px;
}

.w-vision {
  width: 45px;
}

.w-gold-dmg-kp {
  width: 58px;
}
</style>
