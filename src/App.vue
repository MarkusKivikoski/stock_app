<template>
  <v-app id="inspire">
    <v-navigation-drawer
      v-model="drawer"
      fixed
      app
    >
      <v-list dense>


      <v-list-tile>

        <v-text-field 
        v-model="criteria"
        single>
          
        </v-text-field>

      </v-list-tile>

<v-list-tile
v-for="index in filteredRange()"
:key="index"
@click="setActive(index)">
          <v-list-tile-action>
            <v-icon>trending_up</v-icon>
          </v-list-tile-action>
          <v-list-tile-content>
            <!-- Sivupalkin nimilista määrittyy store.ts tiedostoon asetetuista nimistä ja symboleista -->
            <v-list-tile-title>{{getName(index)}}</v-list-tile-title>
          </v-list-tile-content>
        </v-list-tile>    

      </v-list>
    </v-navigation-drawer>
    <v-toolbar color="indigo" dark fixed app>
      <v-toolbar-side-icon @click.stop="drawer = !drawer"></v-toolbar-side-icon>
      <v-toolbar-title>Stock App</v-toolbar-title>
    </v-toolbar>
    <v-content>
      <v-container grid-list-md text-xs-center>
        <v-layout row wrap>
          <Chart 
          v-bind:index='index' 
          v-for="index in range()"
          :key="index"
          v-show="index===active"
          ></Chart>
        </v-layout>
      </v-container>
    </v-content>
  </v-app>
</template>

<script lang="ts">
import HelloWorld from '@/components/HelloWorld.vue';
import Chart from '@/components/Chart.vue';
import SidebarItem from '@/components/SidebarItem.vue';

export default {
  name: 'App',
  components: {
    Chart,
    SidebarItem,
  },
  data() {
    return {
      drawer: null,
      active: 0,
      criteria: '',
    };
  },
  props: {
    source: String,
  },
  methods: {
    //sivupalkista valitaan osake joka halutaan näyttää, diagrammi määräytyy indexin perusteella
    range(){
      return [...Array(this.$store.state.names.length).keys()];
    },
    setActive(index: number){
      this.active = index;
    },
    getName(index: number){
      return this.$store.state.names[index];
    },
    //hakupalkin filtteröinti
    filteredRange(){
      let OG = this.range();
      return OG.map(idx => [idx, this.$store.state.names[idx]]).filter(tuple => this.approved(tuple[1])).map(tuple => tuple[0])
    },
    approved(name: string):boolean{
      return name.toLowerCase().includes(this.criteria.toLowerCase());
    }
  }
};
</script>
