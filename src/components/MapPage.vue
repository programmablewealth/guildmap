<template>
  <PrereqParcels>
    <div>
      <h1>The Citaadel</h1>

      <div style="margin-bottom: 20px;">
        <DataFetcherParcelOwners />
      </div>

      <section style="padding: 0 10px 20px 0;">
        <ParcelDetails
          v-if="selectedParcel"
          :parcel="selectedParcel.parcel"
          :owner="selectedParcel.owner"
          @close="selectedParcelId = null"
        />
      </section>

      <div v-if="parcelsMatchingFilters.numMatches > 0">
        {{ parcelsMatchingFilters.numMatches }} parcels owned by guild members found
        <template v-if="onlySelectedDistricts">
          in districts
          {{ filterDistricts.join(', ') }}
        </template>
      </div>

      <div>
        <label>
          <input
            v-model="onlySelectedDistricts"
            type="checkbox"
          >
          Only show guild districts
        </label>
      </div>
    </div>
    <CitaadelMap
      :mapConfig="mapConfig"
      :parcels="parcelsToDisplay"
      :parcelsMatchingFilters="parcelsMatchingFilters.result"
      :parcelColors="parcelColors"
      :selectedParcel="selectedParcel?.parcel"
      @click:parcel="onClickParcel"
    />
  </PrereqParcels>
</template>

<script>
import { ref, computed } from 'vue'
import useParcels from '@/data/useParcels'
import useParcelOwners from '@/data/useParcelOwners'
import DataFetcherParcelOwners from './DataFetcherParcelOwners.vue'
import PrereqParcels from './PrereqParcels.vue'
import ParcelDetails from './ParcelDetails.vue'
import CitaadelMap from './CitaadelMap.vue'
import guildOwners from '@/data/guildOwners.json'

const DEFAULT_MAP_COLORS = {
  light: {
    colorRoads: '#bbbbbb',
    colorWalls: '#000000',
    colorPaartners: '#bf91ff',
    colorLandmarks: '#FA34F3',
    colorAlchemicaFud: '#C8E4C8',
    colorAlchemicaFomo: '#F0DAD1',
    colorAlchemicaAlpha: '#D7EEEE',
    colorAlchemicaKek: '#F0D9F2',
    colorAlchemica: '#E3E3E3'
  },
  dark: {
    colorRoads: '#4A4A4A',
    colorWalls: '#A3A3A3',
    colorPaartners: '#bf91ff',
    colorLandmarks: '#FA34F3',
    colorAlchemicaFud: '#475C47',
    colorAlchemicaFomo: '#5B4A43',
    colorAlchemicaAlpha: '#476262',
    colorAlchemicaKek: '#533A55',
    colorAlchemica: '#292929'
  }
}

const getDefaultMapConfig = function () {
  return {
    unmatchedDisplay: 'outline', /* or 'hide' */
    showRoads: true,
    showWalls: true,
    showPaartners: true,
    showLandmarks: true,
    showAlchemicaFud: true,
    showAlchemicaFomo: true,
    showAlchemicaAlpha: true,
    showAlchemicaKek: true,
    ...DEFAULT_MAP_COLORS.light,
    useOneAlchemicaColor: false
  }
}

export default {
  components: {
    PrereqParcels,
    DataFetcherParcelOwners,
    ParcelDetails,
    CitaadelMap
  },
  setup () {
    const {
      ownersByParcelId,
      fetchStatus: ownersFetchStatus,
      fetchOwners
    } = useParcelOwners()
    // immediately fetch owners if not loaded
    if (!ownersFetchStatus.value.loaded && !ownersFetchStatus.value.loading) {
      fetchOwners()
    }

    const mapConfig = ref(getDefaultMapConfig())
    const filterOwners = guildOwners
    const filterDistricts = ['18', '19', '10']
    const onlySelectedDistricts = ref(true)

    const { parcelsById, fetchStatus: parcelsFetchStatus } = useParcels()

    const parcelsToDisplay = computed(() => Object.values(parcelsById.value))

    const parcelColors = computed(() => {
      const result = Object.fromEntries(
        parcelsToDisplay.value.map(parcel => [
          parcel.id,
          "#F00"
        ])
      )
      return result
    })

    const parcelsMatchingFilters = computed(() => {
      const ownersLowercase = filterOwners.map(address => address.toLowerCase())

      let numMatches = 0
      const result = Object.fromEntries(
        parcelsToDisplay.value.map(parcel => {
          let show = true
          // filter by district?
          if (onlySelectedDistricts.value) {
            show = filterDistricts.includes(parcel.district)
          }
          if (show) {
            // and filter by owners
            show = ownersLowercase.includes(ownersByParcelId.value[parcel.id])
          }
          if (show) { numMatches++ }
          return [parcel.id, show]
        })
      )
      return { result, numMatches }
    })

    const selectedParcelId = ref(null)
    const selectedParcel = computed(() => {
      const parcelId = selectedParcelId.value
      if (!parcelId) { return null }
      const owner = ownersByParcelId.value[parcelId]
      return {
        parcel: parcelsById.value[parcelId],
        owner
      }
    })

    const selectedParcelPaartnerId = ref(null)

    const onClickParcel = (parcel) => {
      if (parcel.paartner) {
        selectedParcelPaartnerId.value = parcel.paartner
        selectedParcelId.value = null
      } else {
        selectedParcelId.value = parcel.id
        selectedParcelPaartnerId.value = null
      }
    }

    return {
      parcelsFetchStatus,
      mapConfig,
      filterDistricts,
      onlySelectedDistricts,
      parcelsToDisplay,
      parcelsMatchingFilters,
      parcelColors,
      onClickParcel,
      selectedParcelId,
      selectedParcel,
      selectedParcelPaartnerId,
      ownersByParcelId
    }
  }
}
</script>

<style scoped>
</style>
