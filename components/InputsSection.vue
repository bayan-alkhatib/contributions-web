<template>
  <div :class="isOpen ? '' : 'hidden overflow-hidden lg:block'">
    <div class="sort-section">
      <h6 class="text-xs font-bold pb-2">Period:</h6>
      <div class="flex lg:flex-col">
        <PeriodDropdown />
      </div>
    </div>
    <div class="sort-section has-border">
      <h6 class="text-xs font-bold pb-2">Sort by:</h6>
      <div class="flex lg:flex-col">
        <RadioButton
          class="lg:pb-1"
          :input-id="`score`"
          :input-name="`sortby`"
          :label-for="`score`"
          :label-text="`Score`"
          :checked="true"
          :value="'score'"
          @on-sort-by-changed="onSortByChanged"
        />
        <RadioButton
          class="pb-2 contributions-radio"
          :input-id="`commits`"
          :input-name="`sortby`"
          :label-for="`commits`"
          :label-text="`Commits`"
          :value="'commit'"
          @on-sort-by-changed="onSortByChanged"
        />
      </div>
    </div>
    <div class="sort-section has-border">
      <h6 class="text-xs font-bold pb-2">Show:</h6>
      <div class="flex lg:flex-col">
        <RadioButton
          class="lg:pb-1"
          :input-id="`all`"
          :input-name="`show`"
          :label-for="`all`"
          :label-text="`All`"
          :checked="true"
          :value="'all'"
          @on-show-changed="onShowChanged"
        />
        <RadioButton
          class="pb-2 ml-16 lg:ml-0"
          :input-id="`only`"
          :input-name="`show`"
          :label-for="`only`"
          :label-text="`Only JOSA members`"
          :value="'members'"
          @on-show-changed="onShowChanged"
        />
      </div>
    </div>
    <div class="sort-section">
      <h6 class="text-xs font-bold pb-2">Count:</h6>
      <div class="flex lg:flex-col">
        <CheckBox
          class="lg:pb-1"
          :input-id="`commits`"
          :input-name="`count`"
          :label-for="`commits`"
          :label-text="`Commits`"
          :checked="true"
          :value="'commits'"
        />
      </div>
    </div>
  </div>
</template>

<script>
import { mapGetters } from 'vuex'
import RadioButton from './RadioButton.vue'
import CheckBox from './CheckBox.vue'
import PeriodDropdown from './PeriodDropdown.vue'
export default {
  name: 'InputsSection',
  components: {
    RadioButton,
    CheckBox,
    PeriodDropdown,
  },
  props: {
    isOpen: { type: Boolean, required: true },
  },
  computed: {
    ...mapGetters({
      getCurrentPage: 'getCurrentPage',
      getPeriod: 'getPeriod',
      getShow: 'getShow',
      getSortBy: 'getSortBy',
    }),
  },
  methods: {
    async onSortByChanged(sortBy) {
      this.$store.commit('setSortBy', sortBy)
      const response = await this.$axios.get(
        `v1/users?sort_by=${sortBy}&page=${this.getCurrentPage}&period=${this.getPeriod}&contributors=${this.getShow}`
      )
      this.$store.commit('setUsers', response.data.users)
    },
    async onShowChanged(show) {
      this.$store.commit('setCurrentPage', 1)
      const response = await this.$axios.get(
        `v1/users?sort_by=${this.getSortBy}&page=${this.getCurrentPage}&period=${this.getPeriod}&contributors=${show}`
      )
      this.$store.commit('setShow', show)
      this.$store.commit('setUsers', response.data.users)
      this.$store.commit('setPageCount', response.data.totalPages)
    },
  },
}
</script>

<style lang="postcss" scoped>
.has-border {
  border-bottom: 1px dotted rgba(0, 0, 0, 0.3);
}

.sort-section {
  @apply mx-8 pt-6 pb-4;
}

.contributions-radio {
  margin-left: 2.8rem;
  @apply lg:ml-0;
}
</style>
