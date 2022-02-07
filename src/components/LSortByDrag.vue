<template>
  <div
    class="box"
    :class="mousedown ? 'move' : 'pointer'"
    :style="{
      width: width + 'px',
      height: Math.ceil(list.length / n) * itemHeight + 'px',
    }"
    @mouseup="handleMoveUp"
    @mouseleave="handleMoveUp"
  >
    <div
      v-for="(item, index) in list"
      :key="index"
      class="item"
      :style="{
        transition: `transform ${mousedown ? delay : '0'}s`,
        transform: `translate3d(${itemWidth * (item.index % n)}px,${
          itemWidth * Math.floor(item.index / n)
        }px,0)`,
        width: itemWidth + 'px',
        height: itemHeight + 'px',
      }"
      @mousedown="handleMouseDown($event, index)"
      @mousemove="handleMove"
    >
      <slot :scope="{ ...item, index }"></slot>
    </div>
  </div>
</template>

<script>
export default {
  name: "LSortByDrag",
  props: {
    drag: {
      type: Boolean,
      default: false,
    },
    list: {
      type: Array,
      default: () => {
        return [];
      },
    },
    itemWidth: {
      type: String,
      default: "60",
    },
    itemHeight: {
      type: String,
      default: "60",
    },
    width: {
      type: String,
      default: "240",
    },
    delay: {
      type: String,
      default: "0.5",
    },
  },
  data() {
    return {
      n: 4,
      mousedown: false,
      startX: 0,
      startY: 0,
      moveIndex: null,
    };
  },
  methods: {
    handleMouseDown(e, index) {
      if (this.drag) {
        this.mousedown = true;
        this.startX = e.clientX;
        this.startY = e.clientY;
        this.moveIndex = index;
      }
    },
    handleMove(e) {
      if (this.mousedown && this.drag) {
        const x = e.clientX - this.startX;
        const y = e.clientY - this.startY;
        const my =
          y < 0
            ? Math.ceil(y / this.itemHeight)
            : Math.floor(y / this.itemHeight);
        const mx =
          x < 0
            ? Math.ceil(x / this.itemWidth)
            : Math.floor(x / this.itemWidth);
        this.moving(mx, my);
      }
    },
    moving(x, y) {
      const start = this.moveIndex;
      let end = y * this.n + x + start;
      const len = this.list.length - 1;
      this.$nextTick(() => {
        this.list.forEach((item, index) => {
          item.index = index;
          if (start === index) {
            item.index = end >= len ? len : end;
          }
          if (start > end) {
            // 前移动
            if (index >= end && index < start) {
              item.index = index + 1;
            }
          }
          if (start < end) {
            // 后移动
            if (index <= end && index > start) {
              item.index = index - 1;
            }
          }
        });
      });
    },
    handleMoveUp() {
      if (this.drag && this.mousedown) {
        this.mousedown = false;
        const newList = [];
        this.list.forEach((item) => {
          newList[item.index] = item;
        });
        this.$emit("sort", newList);
      }
    },
  },
  created() {
    this.n = Math.floor(
      Number.parseInt(this.width) / Number.parseInt(this.itemWidth)
    );
    this.list.forEach((item, index) => {
      item.index = index;
    });
  },
};
</script>

<style scoped>
.box {
  position: relative;
}
.pointer {
  cursor: pointer;
}
.move {
  cursor: move;
}
.item {
  display: flex;
  justify-content: center;
  align-content: center;
  align-items: center;
  position: absolute;
  top: 0;
  left: 0;
}
</style>
