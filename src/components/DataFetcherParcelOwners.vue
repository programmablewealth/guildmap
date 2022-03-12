<template>
  <DataFetcher
    subject="parcel owners"
    :use="useParcelOwners"
    fetchProperty="fetchOwners"
  />
</template>

<script>
import useParcelOwners from '@/data/useParcelOwners'
import DataFetcher from './DataFetcher.vue'

export default {
  components: {
    DataFetcher
  },
  setup () {
    return {
      useParcelOwners
    }
  }
}
</script>

<style scoped>
</style>
