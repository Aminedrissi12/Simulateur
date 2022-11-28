<template>
  <div>
    <div class="listItem-row" style="justify-content: space-between">
      <h4>{{ name }}</h4>
      <span> {{ consommation }} Wh/j </span>
    </div>
    <div class="listItem-row">
      <div class="image">
        <img :src="image" />
      </div>

      <div class="calcul">
        <div class="calcul-row">
          <button @click="Count('decr')">-</button>
          <input
            @keypress="isNumber($event)"
            type="text"
            v-model="this.itemCount"
            @input="Count('chang')"
          />
          <label>Unités</label>
          <button @click="Count('incr')">+</button>
        </div>

        <div style="margin: 0.5rem 0" class="calcul-row">
          <button @click="Hours('decr')">-</button>
          <input
            @keypress="isNumber($event)"
            type="text"
            v-model="this.itemHours"
            @input="Hours('chang')"
          />
          <label>Heures/j</label>
          <button @click="Hours('incr')">+</button>
        </div>

        <div class="calcul-row">
          <button @click="Energy('decr')">-</button>
          <input
            @keypress="isNumber($event)"
            type="text"
            v-model="this.itemEnergy"
            @input="Energy('chang')"
          />
          <label>Watt</label>
          <button @click="Energy('incr')">+</button>
        </div>
      </div>
    </div>

    <div class="d-btn">
      <button class="btn" @click="remove(id)">remove</button>
    </div>
  </div>
</template>

<script>
export default {
  props: ['id', 'hours', 'energy', 'count', 'image', 'name'],
  emits: ['updateCon', 'remove'],
  data() {
    return {
      consommation: null,
      itemCount: 0,
      itemEnergy: 0,
      itemHours: 0,
    }
  },
  async created() {
    this.itemCount = this.count
    this.itemEnergy = this.energy
    this.itemHours = this.hours

    this.consommation = await (this.count * this.energy * this.hours)
  },
  methods: {
    isNumber(evt) {
      evt = evt ? evt : window.event
      var charCode = evt.which ? evt.which : evt.keyCode
      if (
        charCode > 31 &&
        (charCode < 48 || charCode > 57) &&
        charCode !== 46
      ) {
        evt.preventDefault()
      } else {
        return true
      }
    },
    // ////////////////
    Count(vl) {
      if (vl === 'incr') {
        this.itemCount += 1
      } else if (vl === 'decr') {
        this.itemCount -= 1
        if (this.itemCount === 0) {
          this.itemCount = 1
        }
      } else if (vl === 'chang') {
        this.calculCon()
      }
      this.calculCon()
    },
    Hours(vl) {
      if (vl === 'incr') {
        this.itemHours += 1
      } else if (vl === 'decr') {
        this.itemHours -= 1
        if (this.itemHours === 0) {
          this.itemHours = 1
        }
      } else if (vl === 'chang') {
        this.calculCon()
      }
      this.calculCon()
    },
    Energy(vl) {
      if (vl === 'incr') {
        this.itemEnergy += 1
      } else if (vl === 'decr') {
        this.itemEnergy -= 1
        if (this.itemEnergy === 0) {
          this.itemEnergy = 1
        }
      } else if (vl === 'chang') {
        this.calculCon()
      }

      this.calculCon()
    },
    calculCon() {
      this.consommation = this.itemCount * this.itemEnergy * this.itemHours

      const dataUpdate = {
        vl: +this.id,
        count: this.itemCount,
        hours: this.itemHours,
        energy: this.itemEnergy,
      }

      this.$emit('updateCon', dataUpdate)
    },
    remove(vl) {
      this.$emit('remove', {
        vl: +vl,
        consommation: this.consommation,
      })
    },
  },
}
</script>

<style scoped>
.image > img {
  width: 3rem;
  height: 3rem;
  padding: 0.5rem;
  border-radius: 50%;
  box-shadow: 0 0.5rem 1rem rgba(0, 0, 0, 0.4);
}
.listItem-row {
  display: flex;
  flex-direction: row;
  align-items: center;
  justify-content: space-between;
}
.listItem-row > .image > img {
  background-color: #eee;
}
.calcul {
  width: 12rem;
}
.calcul-row {
  display: flex;
  flex-direction: row;
  justify-content: space-between;
}
input {
  width: 3rem;
}
label {
  width: 5rem;
}
.d-btn {
  display: flex;
  justify-content: center;
  margin-top: 1rem;
}
</style>
