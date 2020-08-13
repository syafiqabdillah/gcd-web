<template>
  <div>
      <b-card>
          <h3>{{location_name}}</h3>
      </b-card>
  </div>
</template>

<script>
export default {
    props: ['location_name', 'sublocations'],
    name: 'LocationCard'
}
</script>

<style>

</style>