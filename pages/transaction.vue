<template>
  <b-container>
    <nuxt-child />
  </b-container>
</template>

<script>
export default {
  head() {
    return {
      title: this.$t('page.transaction.metaTitle'),
    }
  },
}
</script>
