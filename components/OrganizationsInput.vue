<template>
  <div :class="isOpen ? '' : 'hidden overflow-hidden lg:block'">
    <div class="sort-section has-border">
      <h6 class="text-xs font-bold pb-2">Sort by:</h6>
      <div class="flex lg:flex-col">
        <RadioButton
          class="mr-3 lg:pb-2 lg:mr-0"
          :input-id="`Total Stars`"
          :input-name="`sortby`"
          :label-for="`Total Stars`"
          :label-text="`Total Stars`"
          :checked="true"
          :value="'repos_stars'"
          @on-sort-by-changed="onSortByChanged"
        />
        <RadioButton
          class="mr-3 lg:pb-2 lg:mr-0"
          :input-id="`Number of Open Source Repositories`"
          :input-name="`sortby`"
          :label-for="`Number of Open Source Repositories`"
          :label-text="`Number of Open Source Repositories`"
          :value="'repos_num'"
          @on-sort-by-changed="onSortByChanged"
        />
        <RadioButton
          :input-id="`Members`"
          :input-name="`sortby`"
          :label-for="`Members`"
          :label-text="`Members`"
          :value="'org_members'"
          @on-sort-by-changed="onSortByChanged"
        />
      </div>
    </div>
  </div>
</template>

<script>
import { mapGetters } from 'vuex'
export default {
  name: 'OrganizationsInput',
  props: {
    isOpen: { type: Boolean, required: true },
  },
  computed: {
    ...mapGetters({
      getCurrentPage: 'getCurrentPage',
    }),
  },
  methods: {
    async onSortByChanged(sortBy) {
      this.$store.commit('setOrgsSortBy', sortBy)
      const response = await this.$axios.get(
        `v1/orgs?sort_by=${sortBy}&page=${this.getCurrentPage}`
      )
      this.$store.commit('setOrgs', response.data.orgs)
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
</style>
