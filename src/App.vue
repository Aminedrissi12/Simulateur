<template>
  <div class="app">
    <div @click="closeMap" class="zone" v-if="MapZon">
      <button @click="closeMap" class="closeMap">x</button>
      <div class="zone_M">
        <Zone v-on:numberRegion="getZon" />
      </div>
    </div>
    <h1>Simulateur Solairs</h1>

    <div class="content1">
      <div class="kits">
        <img :src="this.kits220[selected - 1].image" />
        <div class="item-info">
          <div class="item-info-title">
            <span> {{ this.kits220[selected - 1].title }} </span>
          </div>

          <div>
            <button @click="decrement">{{ incrementBtn }}</button>
            <select
              v-model="selected"
              @change="select(selected)"
              class="item-info-selected"
            >
              <option
                v-for="selecte12 in Object.keys(this.kits220)"
                :key="selecte12"
              >
                {{ +selecte12 + 1 }}
              </option>
            </select>
            <button @click="increment">{{ decrementBtn }}</button>
          </div>

          <div class="item-info-prix">
            <h2>{{ this.kits220[this.selected - 1].prix }} DH</h2>
          </div>
        </div>
      </div>
    </div>

    <div class="content2">
      <div class="content2-row">
        <div class="Row" style="width: 100%">
          <div class="content_pu">
            PRODUCTION DU KIT
            <span class="color-darck">{{ puissanceCom }} Wc</span>
            <span> (en Wh/j)</span>
          </div>
          <div class="puissance">
            <div class="puissance-1"></div>
            <div :style="widthPuissance" class="puissance-2"></div>
          </div>

          <div class="content_consomation content_pu">
            VOTRE CONSOMMATION
            <span class="color-darck">{{ Consommation }}</span>
            <span> (en Wh/j)</span>
          </div>
          <div class="cnsommation">
            <div class="cnsommation-1"></div>
            <div :style="WidthConsommation" class="cnsommation-2"></div>
          </div>
        </div>
        <div class="Row Row-cursor">
          <div class="cursor_hiver" :style="styleHiver"></div>
          <div class="cursor_été" :style="styleEte"></div>
        </div>
      </div>
      <ruler-cell :scaleEnd="this.scaleEnd" id="rulerCell" />
    </div>
    <div class="d-showMap">
      <button @click="showMap" class="showMap">
        Modifier votre zone : {{ numberZone }}
      </button>
    </div>
    <div class="items">
      <items :items="items" @conso="consomation" />
    </div>
  </div>
</template>

<script>
import items from './components/items.vue'
import Zone from './components/zone.vue'
import rulerCell from './components/rulerCell'
import axios from 'axios'
export default {
  name: 'App',
  data() {
    return {
      backround: '#17f635',
      scaleEnd: 10000,
      calcule: 100,
      numberZone: 1,
      ete: 0,
      hiver: 0,
      scrolWidth: null,
      MapZon: false,
      selected: 1,
      kits12: new Array(),
      kits220: new Array(),
      items: new Array(),
      incrementBtn: '<',
      decrementBtn: '>',
      Consommation: 0,
      zonesData: [
        {
          id: 1,
          pes_hiver: 6,
          pes_ete: 9,
          pes_avg: 0,
        },
        {
          id: 2,
          pes_hiver: 6.6,
          pes_ete: 9.9,
          pes_avg: 0,
        },
        {
          id: 3,
          pes_hiver: 7.5,
          pes_ete: 11,
          pes_avg: 0,
        },
        {
          id: 4,
          pes_hiver: 5,
          pes_ete: 8,
          pes_avg: 0,
        },
        {
          id: 5,
          pes_hiver: 4,
          pes_ete: 7,
          pes_avg: 0,
        },
      ],
      items: [
        {
          id: 1,
          name: 'Lamp',
          energy: 9,
          count: 1,
          image:
            'https://ng-simulator-off-grid.vercel.app/assets/images/lamp.png',
          hours: 5,
        },
        {
          id: 2,
          name: 'TV',
          energy: 80,
          count: 1,
          image:
            'https://ng-simulator-off-grid.vercel.app/assets/images/tv.png',
          hours: 2,
        },
        {
          id: 3,
          name: 'Mobile',
          energy: 5,
          count: 1,
          image:
            'https://ng-simulator-off-grid.vercel.app/assets/images/mobile.png',
          hours: 2,
        },
        {
          id: 4,
          name: 'Laptop',
          energy: 50,
          count: 1,
          image:
            'https://ng-simulator-off-grid.vercel.app/assets/images/laptop.png',
          hours: 3,
        },
        {
          id: 5,
          name: 'Réfrigérateur',
          energy: 20,
          count: 1,
          image:
            'https://ng-simulator-off-grid.vercel.app/assets/images/refrigerator.png',
          hours: 24,
        },
        {
          id: 6,
          name: 'Mixeur',
          energy: 300,
          count: 1,
          image:
            'https://ng-simulator-off-grid.vercel.app/assets/images/blender.png',
          hours: 0.25,
        },
        {
          id: 7,
          name: 'Autre',
          energy: 100,
          count: 1,
          image:
            'https://ng-simulator-off-grid.vercel.app/assets/images/other.png',
          hours: 1,
        },
      ],
      zoneData: null,
    }
  },
  components: {
    rulerCell,
    items,
    Zone,
  },
  async beforeCreate() {
    // get data from the server befre create
    await axios
      .get(`https://api-simulatuer.onrender.com/api/v1/kits`)
      .then(({ data }) => {
        // sort data with puissance
        const vl = data.kits.sort((a, b) => a.puissance - b.puissance)
        //  filter data with tension
        for (const vlue of vl) {
          if (vlue.tension === 12) {
            this.kits12.push(vlue)
          } else if (vlue.tension === 220) {
            this.kits220.push(vlue)
          }
        }
      })
      .catch((err) => {
        return err
      })
    this.handleResize()
  },
  created() {
    this.zoneData = this.zonesData[0]
    window.addEventListener('resize', this.handleResize)
  },
  computed: {
    puissanceCom() {
      return this.kits220[this.selected - 1].puissance * this.zoneData.pes_ete
    },
    widthPuissance() {
      return `width: ${
        (this.kits220[this.selected - 1].puissance * this.zoneData.pes_ete) /
        (this.scaleEnd / 100)
      }%`
    },
    styleEte() {
      return `left: ${this.ete}px`
    },
    styleHiver() {
      return `left: ${this.hiver}px`
    },
    WidthConsommation() {
      return `width: ${this.Consommation / (this.scaleEnd / 100)}%;
              background-color: ${this.backround}
              `
    },
  },
  methods: {
    increment() {
      if (Object.keys(this.kits220).length !== this.selected) {
        this.selected++
      }
      this.select(this.selected)

      this.handleResize()
      this.chickConso()
    },
    decrement() {
      if (this.selected !== 1) {
        this.selected--
      } else {
        this.selected = 1
      }
      this.select(this.selected)

      this.handleResize()
      this.chickConso()
    },
    select(sel) {
      if (sel > 3) {
        this.scaleEnd = 26000
      } else if (sel <= 3) {
        this.scaleEnd = 10000
      }
      this.handleResize()
      this.chickConso()
    },
    closeMap() {
      this.MapZon = false
    },
    showMap() {
      this.MapZon = true
    },
    getZon(vl) {
      const numberZon = this.zonesData.filter((data) => data.id === vl)
      this.zoneData = numberZon[0]
      this.numberZone = numberZon[0].id

      this.handleResize()
      this.chickConso()
    },
    handleResize() {
      const Width = window.document.querySelector('#rulerCell').scrollWidth

      const LeftEte =
        (((this.kits220[this.selected - 1].puissance * this.zoneData.pes_ete) /
          (this.scaleEnd / 100)) *
          Width) /
        100

      const LeftHiver =
        (((this.kits220[this.selected - 1].puissance *
          this.zoneData.pes_hiver) /
          (this.scaleEnd / 100)) *
          Width) /
        100

      this.ete = LeftEte - 17
      this.hiver = LeftHiver - 17
    },
    consomation(vl) {
      this.Consommation = vl
      this.chickConso()
    },
    chickConso() {
      if (
        this.Consommation <
        this.kits220[this.selected - 1].puissance * this.zoneData.pes_hiver
      ) {
        this.backround = '#17f635'
      } else if (
        this.Consommation >
        this.kits220[this.selected - 1].puissance * this.zoneData.pes_ete
      ) {
        this.backround = '#ff0000'
      } else if (
        this.Consommation >
          this.kits220[this.selected - 1].puissance * this.zoneData.pes_hiver ||
        this.Consommation <
          this.kits220[this.selected - 1].puissance * this.zoneData.pes_ete
      ) {
        this.backround = '#f67017'
      }
    },
  },
}
</script>

<style>
#app {
  width: 100%;
  padding: 0 1rem;
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  box-sizing: border-box;
}
h1 {
  text-align: center;
}
.app {
  border: 1px solid #2c3e50;
  padding: 1rem;
}
.content2 {
  margin-top: 3rem;
  padding: 1rem;
  box-shadow: 0 0.5rem 1rem rgba(0, 0, 0, 0.4);
}
.content2-row {
  display: flex;
  flex-direction: row;
}

.content_pu {
  color: rgb(213, 213, 213);
}
.kits {
  display: flex;
  flex-direction: row;
  align-items: center;
}
.kits img {
  width: 10rem;
  height: 10rem;
  padding: 1rem;
  box-shadow: 0 0.5rem 1rem rgba(0, 0, 0, 0.4);
}
.item-info {
  flex: 1;
  text-align: center;
}
.item-info-title {
  margin-bottom: 1rem;
}
.cnsommation,
.puissance {
  position: relative;
  height: 1rem;
  margin-top: 0.5rem;
}
.puissance {
  margin-top: 1rem;
}

.cnsommation-1,
.puissance-1 {
  height: 1rem;
  background: #eaeaea;
}
.cnsommation-2,
.puissance-2 {
  position: absolute;
  top: 0;
  height: 1rem;
  z-index: 100;
  transition: 1s ease-out;
}
.content_consomation {
  margin-top: 0.5rem;
}
.puissance-2 {
  background: #4caffa;
  width: 50%;
}
.cnsommation-2 {
  background: #17f635;
  width: 20%;
}
.color-darck {
  color: #2c3e50;
}
.Row-cursor {
  position: relative;
  right: 100%;
  top: 15px;
}
.Row-cursor > div {
  transition: 1s ease-out;
}
.cursor_hiver {
  position: absolute;
  z-index: 200;
  left: 0px;
  width: 35px;
  height: 131px;
  background: url(https://ng-simulator-off-grid.vercel.app/assets/images/barre_cursor_hiver.svg)
    left top no-repeat;
}
.cursor_été {
  position: absolute;
  z-index: 200;
  left: 120px;
  width: 35px;
  height: 131px;
  background: url(https://ng-simulator-off-grid.vercel.app/assets/images/barre_cursor_ete.svg)
    left top no-repeat;
}
.zone {
  display: flex;
  align-items: center;
  justify-content: center;
  position: fixed;
  background-color: rgba(0, 0, 0, 0.75);
  backdrop-filter: blur(5px);
  z-index: 300;
  top: 0;
  bottom: 0;
  left: 0;
  right: 0;
}
.closeMap {
  font-size: 2rem;
  color: rgb(249 246 246);
  position: absolute;
  z-index: 700;
  right: 9px;
  top: 9px;
  background: none;
  border: none;
  cursor: pointer;
}
.zone_M {
  background-color: #fff;
  padding: 3rem;
  position: absolute;
  z-index: 200;
  /* background-color: aquamarine; */
}
.d-showMap {
  display: flex;
  justify-content: center;
  margin: 1rem 0;
}
</style>
