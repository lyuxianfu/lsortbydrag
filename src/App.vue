<template>
  <button @click="handleSort">{{ sort ? "保存" : "管理" }}</button>
  <LSortByDrag
    :drag="sort"
    width="400"
    item-width="80"
    item-height="80"
    :list="list"
    @itemClick="handleClick"
    @sort="onSort"
  >
    <template v-slot="{ scope }">
      <img
        :src="require('@/assets/reduce.png')"
        class="iconImg"
        v-if="sort"
        @click="handleClick(scope, scope.index)"
      />
      <svg class="icon" style="width: 2.5em; height: 2.5em" aria-hidden="true">
        <use v-bind:xlink:href="scope.icon"></use>
      </svg>
    </template>
  </LSortByDrag>

  <div style="display: flex">
    <div
      v-for="(item, index) in list2"
      :key="index"
      style="width: 80px; height: 80px"
      class="item"
    >
      <img
        :src="require('@/assets/add.png')"
        class="iconImg"
        v-if="sort"
        @click="handleClick2(item, index)"
      />
      <svg class="icon" style="width: 2.5em; height: 2.5em" aria-hidden="true">
        <use v-bind:xlink:href="item.icon"></use>
      </svg>
    </div>
  </div>
</template>

<script>
// import LSortByDrag from "./components/LSortByDrag";
import LSortByDrag from "../lib/LSortByDrag.umd";
export default {
  name: "App",
  components: {
    LSortByDrag,
  },
  data() {
    return {
      list: [
        { icon: "#icon-drink" },
        { icon: "#icon-basket" },
        { icon: "#icon-envelope" },
        { icon: "#icon-bonsai" },
        { icon: "#icon-dragon" },
        { icon: "#icon-bowl" },
        { icon: "#icon-gate" },
        { icon: "#icon-yin-yang" },
        { icon: "#icon-fan" },
        { icon: "#icon-lantern" },
        { icon: "#icon-lantern-" },
        { icon: "#icon-fireworks" },
        { icon: "#icon-china" },
      ],
      list2: [],
      sort: false,
    };
  },
  methods: {
    handleSort() {
      this.sort = !this.sort;
    },
    handleClick(item, index) {
      this.list2.push(item);
      this.list.splice(index, 1);
      this.list.forEach((item, index) => {
        item.index = index;
      });
    },
    handleClick2(item, index) {
      this.list.push(item);
      this.list2.splice(index, 1);
    },
    onSort(list) {
      this.list = list;
      console.log(this.list);
    },
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
}
.item {
  width: 80px;
  height: 80px;
  display: flex;
  justify-content: center;
  align-content: center;
  align-items: center;
  position: relative;
}
.img {
  width: 60px;
  height: 60px;
}
.iconImg {
  width: 20px;
  height: 20px;
  position: absolute;
  right: 5px;
  top: 5px;
}
</style>
