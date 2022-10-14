<template>
  <div class="ruler">
    <div class="ruler-cell">
      <span class="zero">0</span>
      <span class="mid1">{{ scaleEnd / 5 }}</span>
      <span class="mid2">{{ (scaleEnd / 5) * 2 }} </span>
      <span class="mid3">{{ (scaleEnd / 5) * 3 }}</span>
      <span class="mid4">{{ (scaleEnd / 5) * 4 }}</span>
      <span class="number">{{ scaleEnd }} Kw</span>
    </div>
  </div>
</template>

<script>
export default {
  name: 'rulerCell',
  props: ['scaleEnd'],
  created() {},
}
</script>

<style scoped>
.ruler {
  margin: 2em 0 auto;
  background-color: rgb(255, 255, 223);
  height: 20px;
  width: 100%;
  display: flex;
  justify-content: center;
}
.ruler-cell {
  position: relative;
  height: 100%;
  width: calc(100%);
  border-left: 1px solid black;
  border-right: 1px solid black;
  background-repeat: no-repeat;
  background-size: 100% 1px, 1px 8px, 1px 8px, 1px 8px, 1px 8px;
  background-position: 0% center, 20% center, 40% center, 60% center, 80% center;
  background-image: linear-gradient(gray, gray), linear-gradient(gray, gray),
    linear-gradient(gray, gray), linear-gradient(gray, gray),
    linear-gradient(gray, gray);
}
.ruler-cell > span {
  position: absolute;
  top: -15px;
  font-size: 12px;
  text-align: center;
  font-weight: 400;
}
.zero {
  left: 0;
  transform: translateX(-50%);
}
.mid1 {
  right: 80%;
}
.mid2 {
  right: 60%;
}
.mid3 {
  right: 40%;
}
.mid4 {
  right: 20%;
}
.number {
  right: 0px;
  /* transform: translateX(0%); */
}
</style>
