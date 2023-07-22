<template>
  <div class="container mx-auto flex flex-col items-center bg-gray-100 p-4">
    <div v-if="isLoading" class="fixed w-100 h-100 opacity-80 bg-purple-800 inset-0 z-50 flex items-center justify-center">
      <svg class="animate-spin -ml-1 mr-3 h-12 w-12 text-white" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24">
        <circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle>
        <path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"></path>
      </svg>
    </div>
    <div class="container">
      <section>
        <div class="flex justify-between items-center">
          <div class="max-w-xs">
            <label for="wallet" class="block text-sm font-medium text-gray-700"
            >Тикер</label
            >
            <div class="mt-1 relative rounded-md shadow-md">
              <input
                  v-model="tickerInput"
                  @keydown.enter="add(tickerInput)"
                  type="text"
                  name="wallet"
                  id="wallet"
                  class="block w-full pr-10 p-2 border-gray-300 text-gray-900 focus:outline-none focus:ring-gray-500 focus:border-gray-500 sm:text-sm rounded-md"
                  placeholder="Например DOGE"
              />
            </div>
            <div class="flex bg-white shadow-md p-1 rounded-md shadow-md flex-wrap">
            <span v-for="name in hotButtons" :key="name" @click="add(name)" class="inline-flex items-center px-2 m-1 rounded-md text-xs font-medium bg-gray-300 text-gray-800 cursor-pointer">
              {{ name }}
            </span>
            </div>
            <div v-if="isExist" class="text-sm text-red-600">Такой тикер уже добавлен</div>
          </div>
          <img width="96" src="../public/logo.svg.webp" />
        </div>
        <button
            @click="add(tickerInput)"
            type="button"
            class="my-4 inline-flex items-center py-2 px-4 border border-transparent shadow-sm text-sm leading-4 font-medium rounded-full text-white bg-gray-600 hover:bg-gray-700 transition-colors duration-300 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-gray-500"
        >
          <svg
              class="-ml-0.5 mr-2 h-6 w-6"
              xmlns="http://www.w3.org/2000/svg"
              width="30"
              height="30"
              viewBox="0 0 24 24"
              fill="#ffffff"
          >
            <path
                d="M13 7h-2v4H7v2h4v4h2v-4h4v-2h-4V7zm-1-5C6.48 2 2 6.48 2 12s4.48 10 10 10 10-4.48 10-10S17.52 2 12 2zm0 18c-4.41 0-8-3.59-8-8s3.59-8 8-8 8 3.59 8 8-3.59 8-8 8z"
            ></path>
          </svg>
          Добавить
        </button>
      </section>

      <hr v-if="tickers.length > 0" class="w-full border-t border-gray-600 my-4" />
      <dl class="mt-5 grid grid-cols-1 gap-5 sm:grid-cols-3">
        <TickerItem v-for="t in tickers" @click="selectTicker(t)" :key="t.name" :name="t.name" :price="t.value" v-on:delete="onDelete"></TickerItem>
      </dl>
      <hr v-if="!!selectedTicker" class="w-full border-t border-gray-600 my-4" />
      <section v-if="!!selectedTicker" class="relative">
        <h3 class="text-lg leading-6 font-medium text-gray-900 my-8">
          {{ selectedTicker.name }} - USD
        </h3>
        <div class="flex items-end border-gray-600 border-b border-l h-64">
          <div
              class="bg-purple-800 border w-10"
              v-for="(bar,i) in normalizeGraph()"
              :key="i"
              :style="{ height: `${bar}%` }"
          ></div>
        </div>
        <button
            type="button"
            class="absolute top-0 right-0"
            @click="removeSelectedTicker()"
        >
          <svg
              xmlns="http://www.w3.org/2000/svg"
              xmlns:xlink="http://www.w3.org/1999/xlink"
              xmlns:svgjs="http://svgjs.com/svgjs"
              version="1.1"
              width="30"
              height="30"
              x="0"
              y="0"
              viewBox="0 0 511.76 511.76"
              style="enable-background:new 0 0 512 512"
              xml:space="preserve"
          >
          <g>
            <path
                d="M436.896,74.869c-99.84-99.819-262.208-99.819-362.048,0c-99.797,99.819-99.797,262.229,0,362.048    c49.92,49.899,115.477,74.837,181.035,74.837s131.093-24.939,181.013-74.837C536.715,337.099,536.715,174.688,436.896,74.869z     M361.461,331.317c8.341,8.341,8.341,21.824,0,30.165c-4.16,4.16-9.621,6.251-15.083,6.251c-5.461,0-10.923-2.091-15.083-6.251    l-75.413-75.435l-75.392,75.413c-4.181,4.16-9.643,6.251-15.083,6.251c-5.461,0-10.923-2.091-15.083-6.251    c-8.341-8.341-8.341-21.845,0-30.165l75.392-75.413l-75.413-75.413c-8.341-8.341-8.341-21.845,0-30.165    c8.32-8.341,21.824-8.341,30.165,0l75.413,75.413l75.413-75.413c8.341-8.341,21.824-8.341,30.165,0    c8.341,8.32,8.341,21.824,0,30.165l-75.413,75.413L361.461,331.317z"
                fill="#718096"
                data-original="#000000"
            ></path>
          </g>
        </svg>
        </button>
      </section>
    </div>
  </div>
</template>

<script>

import {TICKERS_KEY} from "@/shared/constants/localStorageKeys";
import TickerItem from "./components/TickerItem.vue";

const hotButtons = [
  'BTC',
  'DOGE',
  'BCH',
  'CHD',
];

export default {
  name: 'App',

  data() {
    return {
      tickerInput: '',
      selectedTicker: null,
      tickers: [],
      graph: [],
      isExist: false,
      isLoading: true,
      hotButtons,
      intervals: {},
    }
  },

  methods: {
      add(name) {
        if (!name) {
          return;
        }

        if (this.tickers.some((ticker) => ticker.name === name)) {
          this.isExist = true;
          return;
        }

        const currentTicker = {
          name,
          value: "-",
        }

        this.intervals[name] = this.loadData(name);
        this.tickers.push(currentTicker);
        this.isExist = false;
      },
      onDelete(name) {
        this.tickers = this.tickers.filter((ticker) => ticker.name !== name);
        if (this.selectedTicker?.name === name) {
          this.selectedTicker = null;
        }
        clearInterval(this.intervals[name]);
        delete this.intervals[name];
        this.persistData();
      },
      selectTicker(ticker) {
        this.selectedTicker = ticker;
        this.graph = [];
      },
      removeSelectedTicker() {
        this.selectedTicker = null;
        this.graph = [];
      },
      normalizeGraph() {
        const max = Math.max(...this.graph);
        const min = Math.min(...this.graph);
        return this.graph.map((price) => {
          const percent = 5 + (((price - min) * 95) / (max - min));
          return percent ? percent : 100;
        })
      },
      loadData(name) {
        return setInterval(async () => {
          try {
            const response = await fetch(`https://min-api.cryptocompare.com/data/price?fsym=${name}&tsyms=USD&api_key=${process.env.VUE_APP_CRYPTO_API_KEY}`);
            const data = await response.json();
            const ticker = this.tickers.find((ticker) => ticker.name === name);

            const value = data.USD > 1 ? data.USD.toFixed(2) : data.USD.toPrecision(2);
            ticker.value = value;
            if (this.selectedTicker?.name === name) {
              this.graph.push(+value)
            }
            this.persistData();
          } catch(error) {
            this.onDelete(name);
            clearInterval(this.intervals[name]);
          }
        },5000)
      },
      persistData() {
        localStorage.setItem(TICKERS_KEY, JSON.stringify(this.tickers))
      }
  },

  beforeMount() {
    setTimeout(() => {
      this.tickers = JSON.parse(localStorage.getItem(TICKERS_KEY)) || [];
      this.tickers.forEach((ticker) => {
        this.intervals[ticker.name] = this.loadData(ticker.name);
      })
      if(this.tickers.length > 0) {
        this.selectedTicker = this.tickers[0];
      }
      this.isLoading = false;
    },500)

  },

  beforeUnmount() {
    Object.values(this.intervals).forEach((interval) => clearInterval(interval));
  },

  components: {
    TickerItem,
  }
}
</script>
