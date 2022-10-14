<template>
  <div class="main">
    <div class="scroul-items">
      <div class="main-it">
        <div
          class="item"
          @click="addItem(item)"
          v-for="item in items"
          :key="item.id"
        >
          <div class="image">
            <img :src="item.image" />
          </div>
          <div>
            <h4 style="margin: 1.2rem 0 0 0">{{ item.name }}</h4>
          </div>
        </div>
      </div>
    </div>

    <div v-if="activCom" class="items-con">
      <button @click="swipeLeft">left</button>
      <div class="listItem-s" ref="content">
        <div
          class="listItem"
          v-for="vl in Object.keys(this.listItems)"
          :key="this.listItems[+vl].id"
        >
          <list-items
            @update-con="updateCon"
            @remove="Remove"
            :id="vl"
            :name="this.listItems[+vl].name"
            :image="this.listItems[+vl].image"
            :count="this.listItems[+vl].count"
            :energy="this.listItems[+vl].energy"
            :hours="this.listItems[+vl].hours"
          />
        </div>
      </div>
      <button @click="swipeRight">right</button>
    </div>
  </div>
</template>

<script>
import listItems from './listItems'
export default {
  props: ['items'],
  emits: ['conso'],
  components: {
    listItems,
  },
  data() {
    return {
      listItems: [],
      consommation: 0,
      activ: false,
    }
  },
  computed: {
    activCom() {
      return this.consommation === 0
        ? (this.activ = false)
        : (this.activ = true)
    },
  },
  methods: {
    addItem(vl) {
      const cons = vl.count * vl.energy * vl.hours
      this.consommation = cons + this.consommation
      this.$emit('conso', this.consommation)

      this.listItems.unshift({
        id: new Date(),
        name: vl.name,
        energy: vl.energy,
        count: vl.count,
        image: vl.image,
        hours: vl.hours,
      })
    },
    updateCon(data) {
      this.listItems[data.vl].count = data.count
      this.listItems[data.vl].energy = data.energy
      this.listItems[data.vl].hours = data.hours

      this.calculCons()
    },
    Remove(data) {
      this.consommation = this.consommation - data.consommation
      this.listItems.length - 1
      delete this.listItems[data.vl]
      this.calculCons()
    },
    calculCons() {
      let i = 0
      this.listItems.forEach((vl) => {
        i = vl.count * vl.energy * vl.hours + i
      })

      this.consommation = i

      this.$emit('conso', this.consommation)
    },
    // //////////////////////////////
    swipeLeft() {
      const content = this.$refs.content
      this.scrollTo(content, -300, 800)
    },
    swipeRight() {
      const content = this.$refs.content
      this.scrollTo(content, 300, 800)
    },
    scrollTo(element, scrollPixels, duration) {
      const scrollPos = element.scrollLeft
      // Condition to check if scrolling is required
      if (
        !(
          (scrollPos === 0 || scrollPixels > 0) &&
          (element.clientWidth + scrollPos === element.scrollWidth ||
            scrollPixels < 0)
        )
      ) {
        // Get the start timestamp
        const startTime =
          'now' in window.performance ? performance.now() : new Date().getTime()

        function scroll(timestamp) {
          //Calculate the timeelapsed
          const timeElapsed = timestamp - startTime
          //Calculate progress
          const progress = Math.min(timeElapsed / duration, 1)
          //Set the scrolleft
          element.scrollLeft = scrollPos + scrollPixels * progress
          //Check if elapsed time is less then duration then call the requestAnimation, otherwise exit
          if (timeElapsed < duration) {
            //Request for animation
            window.requestAnimationFrame(scroll)
          } else {
            return
          }
        }
        //Call requestAnimationFrame on scroll function first time
        window.requestAnimationFrame(scroll)
      }
    },
  },
}
</script>

<style scoped>
.main {
  padding: 16px;
  display: flex;
  flex-direction: column;
  align-items: center;
  flex-direction: column;
  justify-content: space-evenly;
  box-shadow: 0 0.5rem 1rem rgba(0, 0, 0, 0.4);
}
.scroul-items {
  overflow-x: scroll;
  width: 100%;
  margin-bottom: 1rem;
}
.main-it {
  display: flex;
  flex-direction: row;
}
.item:hover {
  background-color: #eee;
}
.item {
  display: flex;
  flex-direction: column;
  align-items: center;
}
.image {
  width: 6rem;
  height: 4rem;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
}
.image > img {
  width: 3rem;
  height: 3rem;
  padding: 0.5rem;
  border-radius: 50%;
  box-shadow: 0 0.5rem 1rem rgba(0, 0, 0, 0.4);
}
.listItem-s {
  display: flex;
  flex-direction: row;
  overflow-x: scroll;
  width: 100%;
}
.listItem {
  background-color: rgb(227, 227, 227);
  padding: 1rem;
  margin: 0 1rem;
  border-radius: 16px;
  width: 18rem;
}
.items-con {
  display: flex;
  flex-direction: row;
  width: 100%;
}
</style>
