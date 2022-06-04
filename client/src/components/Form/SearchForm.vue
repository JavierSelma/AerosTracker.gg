<template>
  <form
    @submit.prevent="formSubmit"
    :class="{'max-w-lg': !homepage}"
    class="flex self-start w-full h-full text-lg text-teal-100"
  >
    <div
      v-if="open"
      @click="open = false"
      :style="{opacity: homepage ? 0 : 0.9}"
      class="fixed inset-0 z-20 bg-gray-900"
    ></div>
    <div class="relative w-full">
      <input
        ref="input"
        v-model="summoner"
        @focus="open = true"
        :class="dropdown ? 'bg-blue-1000' : 'input-color'"
        class="relative z-30 w-full py-4 pl-6 pr-32 font-bold placeholder-teal-100 placeholder-opacity-75 rounded-lg outline-none focus:bg-blue-1000 summoner-input bypass-click"
        spellcheck="false"
        type="text"
        placeholder="Search Summoner"
      />
      <button
        ref="submit"
        class="absolute right-0 z-40 w-12 h-full hover:text-teal-200"
        type="submit"
      >

      </button>
      <div ref="region-dropdown">
        <SearchFormRegion
          @toggle="dropdown = !dropdown"
          :dropdown="dropdown"
          :homepage="homepage"
        />
      </div>
      
    </div>
  </form>
</template>

<script>
import { mapState } from 'vuex'
import SearchFormRegion from '@/components/Form/SearchFormRegion.vue'

export default {
  components: {
    SearchFormRegion
  },

  props: {
    homepage: {
      type: Boolean,
      default: false
    }
  },

  data() {
    return {
      summoner: '',
      dropdown: false,
      open: false,
    }
  },

  computed: {
    ...mapState({
      selectedRegion: state => state.settings.region
    }),
  },

  watch: {
    open(newVal) {
      // Search Dropdown open
      if (newVal) {
        this.dropDownOpening()
      } else {
        this.dropDownClosing()
      }
    },
    $route(newRoute) {
      this.summoner = newRoute.params.name
      this.dropdown = false
      this.open = false
    }
  },

  created() {
    if (!this.summoner.length && !this.homepage) {
      this.summoner = this.$route.params.name
    }
    window.addEventListener('blur', this.windowBlur)
    window.addEventListener('keydown', this.handleEscape)
  },

  beforeDestroy() {
    window.removeEventListener('blur', this.windowBlur)
    window.removeEventListener('keydown', this.handleEscape)
    this.dropDownClosing()
  },

  methods: {
    dropDownClosing() {
      const header = document.querySelector('.header div')
      if (!this.homepage && header) {
        header.style.paddingRight = 0
      }
      document.body.style.marginLeft = 0
      document.body.style.overflow = 'auto'
    },
    dropDownOpening() {
      const header = document.querySelector('.header div')
      if (!this.homepage) {
        document.body.style.marginLeft = `-${this.getScrollbarWidth()}px`
        header.style.paddingRight = `${this.getScrollbarWidth()}px`
      }
      document.body.style.overflow = 'hidden'
    },
    formSubmit() {
      const search = this.summoner.split(' ').join('').replace('+', ' ')
      if (search.length) {
        this.$emit('formSubmit', search, this.selectedRegion)
      }
    },
    getScrollbarWidth() {
      const outer = document.createElement('div')
      outer.style.visibility = 'hidden'
      outer.style.overflow = 'scroll' // forcing scrollbar to appear
      outer.style.msOverflowStyle = 'scrollbar' // needed for WinJS apps
      document.body.appendChild(outer)

      const inner = document.createElement('div')
      outer.appendChild(inner)
      const scrollbarWidth = (outer.offsetWidth - inner.offsetWidth)

      outer.parentNode.removeChild(outer)

      return scrollbarWidth
    },
    handleEscape(e) {
      if (e.key === 'Esc' || e.key === 'Escape') {
        this.dropdown = false
        this.open = false
      } else if ((e.key === 'k' && (e.ctrlKey || e.metaKey)) || e.key === '/') {
        e.preventDefault()
        this.dropdown = false
        this.open = !this.open
      }
    },
    windowBlur() {
      this.open = false
    },
  }
}
</script>

<style scoped>
.summoner-input::placeholder {
  @apply font-normal;
}
</style>
