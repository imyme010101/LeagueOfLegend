
<template>
  <div class="flex flex-col min-h-screen bg-c-2">
    <SVGContainer />
    <NotificationsContainer />
    <RunesContainer />

    <header
      class="fixed w-full px-10 z-20 text-gray-100 transition-colors duration-100 ease-in-out bg-c-0"
    >
      <div class="flex items-center h-16 gap-8">
        <h1>
          <a href="/" class="flex flex-col items-center justify-center text-c-3 py-2 px-3 rounded-sm">
            <span class="h-0 text-z">{{ title }}</span>
            <div
              class="logo text-c-1" 
            >
              ZEM
            </div>
            <span class="text-xs text-c-2">zem.gg</span>
          </a>
        </h1>

        <div class="flex justify-between flex-1 h-full">
          <div class="flex">
            <nuxt-link
              v-if="$route.params.name"
              :to="{ name: 'summoner-region-name-mode', params: { region: $route.params.region, name: $route.params.name }}"
              class="z-20 flex items-center justify-center px-4 text-xs font-bold gap-2 menu h-full"
              exact
            >
              <span>소환사 정보</span>
            </nuxt-link>

            <nuxt-link
              :to="{ name: 'multiple-region-names', params: { region: $route.params.region }}"
              class="z-20 flex items-center justify-center px-4 text-xs font-bold gap-2 menu h-full"
            >
              <span>AI 멀티서치</span>
            </nuxt-link>
          </div>
          
          <div class="flex flex-1 items-center justify-center">
            <SearchForm @formSubmit="redirect" :homepage="false" />
          </div>
          
          <div>
            <a
              href="https://discord.gg/W76QeWsPCM"
              target="_blank"
              class="z-20 flex items-center justify-center text-sm font-bold gap-2 h-full"
            >
              <div class="flex items-center justify-center">
                <nuxt-img :src="`https://zem.gg/img/Discord.png`" format="webp" width="32" height="28" class="w-8 h-6" />
              </div>
              디코 참여
            </a>
          </div>
          <!-- <nuxt-link
            :to="{ name: 'multiple-region-names', params: { region: selectedRegion }}"
            class="z-20 flex flex-col items-center justify-center w-full px-4 py-4 text-xs font-bold gap-2 menu"
          >
            <div class="w-10 h-10 rounded-full flex items-center justify-center font-bold font-gong">
              M
            </div>
            <span>멀티 서치</span>
          </nuxt-link> -->
        </div>
          <!-- <nuxt-link
            :to="{ name: 'multiple', params: { region: $route.params.region }}"
            class="flex items-center justify-center text-teal-100 h-full px-4"
          >
            <img src="~/img/icon/multiple.svg" class="w-5 h-5 rounded-full bg-white flex items-center justify-center" />
            <span class="ml-2">소환사 멀티서치</span>
          </nuxt-link> -->
        
      </div>
    </header>

    <div
      class="pt-20"
    >
      <nuxt />
      <MainFooter />
    </div>
  </div>
</template>


<script>
import { mapActions, mapState, mapGetters } from 'vuex'

import NotificationsContainer from '@/components/Global/NotificationsContainer.vue'
import RunesContainer from '@/components/Match/Runes/RunesContainer.vue'
import SVGContainer from '@/components/Global/SVGContainer.vue'

import SearchForm from '@/components/Form/SearchForm.vue'
import MainFooter from '@/components/Layout/MainFooter.vue'

export default {
  components: {
    MainFooter,
    SearchForm,
    NotificationsContainer,
    RunesContainer,
    SVGContainer,
  },

  data() {
    return {
      bgHeader: false
    }
  },

  computed: {
    title() {
      return this.summonerFound ? `${this.basic.account.name} | 소환사 전적 검색, 인분 검색, 등급 검색 ZEM.GG` : `${this.$route.params.name} |  소환사 전적 검색, 인분 검색, 등급 검색 ZEM.GG`
    },
    tabTransition() {
      return this.summonerFound && this.overviewLoaded ? 'tab' : 'none'
    },
    ...mapState({
      basic: state => state.summoner.basic,
      current: state => state.summoner.live.match,
      favorites: state => state.settings.favorites,
      selectedRegion: state => state.settings.region
    }),
    ...mapGetters('summoner', ['playing', 'overviewLoaded', 'summonerFound', 'summonerNotFound', 'summonerLoading'])
  },
  
  beforeMount() {
    this.updateSettings({name: 'region'})
    this.updateSettings({name: 'recentSearches', isJson: true})
    this.updateSettings({name: 'favorites', isJson: true})
  },

  methods: {
    redirect(summoner, region) {
      this.$router.push(`/summoner/${region}/${summoner}`)
    },

    ...mapActions('settings', ['updatePercent', 'updateSettings'])
  }
  // async asyncData({ store, params }) {
  //   await store.dispatch('settings/updateSettings', { name: 'region', value: params.region.toLowerCase() })
  // }
}
</script>

<style scoped>
.menu {
  border-bottom: 4px solid transparent;
}


.nuxt-link-active {
  border-bottom: 4px solid #ff9600;
      color: #ff9600;
}

.logo {
  letter-spacing: 3px;
  display: inline;
  font-size: 1em;
  line-height: 100%;
  font-weight: bold;
  font-family: 'GongGothicBold';
}
</style>