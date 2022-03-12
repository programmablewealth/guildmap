<template>
  <div
    v-if="parcel"
    class="parcel-details"
  >
    <button
      type="button"
      style="float: right; display: flex;"
      @click="$emit('close')"
    >
      Close
    </button>

    <h2>Parcel details:</h2>

    ID: {{ parcel.id }}
    <br>Name: {{ parcel.parcelHash }}
    <br>Size: {{ parcel.sizeLabel }}
    <br>District: {{ parcel.district }}

    <div v-if="owner">
      Owner: {{ owner }}
    </div>

    <div v-if="discord">
      Discord: {{ discord }}
    </div>
  </div>
</template>

<script>
export default {
  props: {
    parcel: { type: Object, required: true },
    owner: { type: String, default: null },
    discord: { type: String, default: null }
  }
}
</script>

<style scoped>
  .parcel-details {
    border: 1px solid #ccc;
    border-radius: 10px;
    padding: 10px 10px 15px 15px;
  }
</style>
