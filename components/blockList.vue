<template>
  <block-list-table
    :items="items"
    :paginated="paginated"
    loader
    :is-loading="isLoading"
  />
</template>

<script>
import { mapActions, mapState } from 'vuex'
import {
  BLOCK_LIST,
  BLOCK_MODULE_NAMESPACE,
  FETCH_BLOCK_LIST,
} from '@/store/block'
import BlockListTable from '@/components/blockListTable'

export default {
  components: { BlockListTable },
  props: {
    page: { type: Number, default: 1 },
    paginated: { type: Boolean, default: false },
    loader: { type: Boolean, default: false },
    refresh: { type: Boolean, default: false },
    refreshInterval: { type: Number, default: 15000 },
  },
  data() {
    return {
      currentPage: 1,
      isLoading: false,
      fetchBlockTask: null,
    }
  },
  async fetch() {
    this.isLoading = true
    await this.$store.dispatch(
      `${BLOCK_MODULE_NAMESPACE}/${FETCH_BLOCK_LIST}`,
      { page: this.$props.page }
    )
    this.isLoading = false
  },
  computed: {
    ...mapState(BLOCK_MODULE_NAMESPACE, {
      items: BLOCK_LIST,
    }),
  },
  watch: {
    async page(val) {
      this.currentPage = val
      this.isLoading = true
      await this.fetchList({ page: this.currentPage })
      this.isLoading = false
    },
  },
  mounted() {
    if (this.refresh) {
      this.fetchBlockTask = setInterval(() => {
        this.fetchList({ page: this.currentPage })
      }, this.refreshInterval)
    }
  },
  beforeDestroy() {
    if (this.fetchBlockTask !== null) {
      clearInterval(this.fetchBlockTask)
    }
  },
  methods: {
    ...mapActions(BLOCK_MODULE_NAMESPACE, {
      fetchList: FETCH_BLOCK_LIST,
    }),
  },
}
</script>
