<template>
 <v-list-tile @click="$emit('setFocus', 2)">
          <v-list-tile-action>
            <v-icon>trending_up</v-icon>
          </v-list-tile-action>
          <v-list-tile-content>
            <v-list-tile-title>{{getName()}}</v-list-tile-title>
          </v-list-tile-content>
        </v-list-tile>    
</template>

<script lang="ts">
import Vue from 'vue'
export default Vue.extend({
    props: {
    index: Number,
  },
  methods: {
    getName(){
      return this.$store.state.names[this.index];
    },
  }
})

</script>

<style>

</style>
