<template>
  <v-flex xs6>
    <v-card>
      <line-chart :chart-data="datacollection"></line-chart>
      <div>
        <v-card-title primary-title>
          <div>
            <h3 class="headline mb-0">{{ type }}</h3>
            
          </div>
        </v-card-title>
        <v-card-actions>
          <v-btn @click="fillStockData()" flat color="orange">Refresh</v-btn>
        </v-card-actions>
      </div>
    </v-card>
  </v-flex>
</template>

<script lang="ts">
import LineChart from '@/components/LineChart.js';
import axios from 'axios';

export default {
  components: {
    LineChart,
  },

  props: {
    index: Number,
  },

  data() {
    return {
      datacollection: null,
      type: 'DATA',
      symbol: this.$store.state.symbols[this.index],
      name: this.$store.state.names[this.index],
    };
  },
  mounted() {
    this.fillStockData();
  },
  methods: {
    setData(thenumbers: number[], thelabels: string[], headline: string): void {
      this.datacollection = {
        labels: thelabels,
        datasets: [
          {
            label: headline,
            backgroundColor: '#f17119',
            borderColor: '#f17119',
            data: thenumbers,
            fill: false,
          },
        ],
      };

      this.type=headline
    },
    fillRandomData(): void {
      this.type = 'RANDOM';
      const dump = this.getRandomIntArray(30);
      this.setData(dump, dump.map(p => String(p)), this.symbol);
    },
    getRandomInt() {
      return Math.floor(Math.random() * (50 - 5 + 1)) + 5;
    },
    getRandomIntArray(n: number): number[] {
      const arr = [];
      for (let i = 0; i < n; i++) {
        arr.push(this.getRandomInt());
      }
      return arr;
    },

    /*
    funktio hakee apista symbolin avulla osakedataa ja piirtää sen diagrammiin.
    ohjelma käyttää axiosta api kutsussa.
    */
    fillStockData(): void {
      this.type = 'STOCK';
      const fetcher = axios({
        method: 'get',
        url: 'https://www.alphavantage.co/query',
        params: {
          function: 'TIME_SERIES_INTRADAY',
          symbol: this.symbol,
          interval: '60min',
          apikey: 'HX7K9KL65T31O9FH',
        },
      }).then((res) => {
        const root = res.data['Time Series (60min)'];
        const prices = Object.keys(root).map((key, index) => {
          return Number(root[key]['1. open']);
        });
        this.setData(prices, Object.keys(root).map(str => str.substr(0, 11)), this.name);
      }).catch((e) => {
        return e;
      });
      //ilman fillRandomData funktiota diagrammit jäävät tyhjäksi, koska ohjelma ei ehdi hakemaan tarkkaa tietoa ennen diagrammin piirtämistä. Korjattavissa.
      this.fillRandomData();
    },
  },
};
</script>

<style>
  .small {
    max-width: 600px;
    margin:  150px auto;
  }
</style>
