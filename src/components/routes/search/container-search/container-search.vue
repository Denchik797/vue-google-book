<template>
  <form
    :class="rootClasses"
    @submit.prevent="handleSubmit"
  >
    <input-search
      :value="queryString"
      :is-active="isSearchTipsExist"
      :is-searching="isSearchPerforming"
      @search-input="debouncedHandleSearch"
      @cross-click="handleCrossClick"
      @down-press="handleDownPress"
      @up-press="handleUpPress"
    />
    <list-search-tips :search-tips="searchTips" />
  </form>
</template>

<script>
import { mapGetters, mapActions } from 'vuex';
import debounce from 'lodash.debounce';

import { InputSearch } from '../input-search';
import { ListSearchTips } from '../list-search-tips';

export default {
  name: 'container-search',
  components: {
    InputSearch,
    ListSearchTips,
  },
  created() {
    this.debouncedHandleSearch = debounce(this.handleSearch, 250);
  },
  computed: {
    ...mapGetters('library', [
      'queryString',
      'searchTips',
      'isSearchPerforming',
      'isSearchTipsExist',
    ]),

    rootClasses() {
      const base = 'container-search';

      return [
        base,
        this.queryString && this.isSearchTipsExist
          ? `${base}--search-tips-active`
          : null,
      ];
    },
  },
  methods: {
    ...mapActions('library', [
      'performSearch',
      'resetQueryString',
      'resetSearchTips',
    ]),

    async handleSearch(query) {
      if (query) {
        await this.performSearch({ query });
      } else {
        // remove tips results in case of saved tips when input is empty
        await this.resetSearchTips();
      }
    },
    async handleCrossClick() {
      await this.resetQueryString();
      await this.resetSearchTips();
    },
    handleSubmit() {
      console.info('search btn clicked');
    },
    handleDownPress() {
      console.info('press down');
    },
    handleUpPress() {
      console.info('press up');
    },
  },
};
</script>

<style scoped lang="scss">
  @import 'container-search';
</style>