<template>
  <v-app>
    <v-toolbar app>
      <v-toolbar-title class="headline text-uppercase">
        <span>Traffic</span>
        <span class="font-weight-light"> Data Visualization</span>
      </v-toolbar-title>
      <v-spacer></v-spacer>
      <v-btn
        flat
        href="https://d3js.org/"
        target="_blank"
      >
        <span class="mr-2">Baseon D3</span>
      </v-btn>
    </v-toolbar>

    <v-content>
      <D3_HeatMap/>
    </v-content>
  </v-app>
</template>

<script>
import D3_HeatMap from './components/d3_heatmap'

export default {
  name: 'App',
  components: {
    D3_HeatMap
  },
  data () {
    return {
      //
    }
  }
}
</script>
