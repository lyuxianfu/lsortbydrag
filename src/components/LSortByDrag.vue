<template>
  <div
    class="box"
    :class="mousedown ? 'move' : 'pointer'"
    style="user-select: none"
    :style="{
      width: width + 'px',
      height: Math.ceil(list.length / n) * itemHeight + 'px',
    }"
    @mouseup="handleMoveUp"
    @mouseleave="handleMoveUp"
    @touchend="handleMoveUp"
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
      @touchstart="handleMouseDown($event, index)"
      @mousemove="handleMove"
      @touchmove="handleMove"
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
    data: {
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
      list: [],
    };
  },
  methods: {
    handleMouseDown(e, index) {
      if (this.drag) {
        this.mousedown = true;
        if (this.isMobile()) {
          this.startX = e.changedTouches[0].clientX;
          this.startY = e.changedTouches[0].clientY;
        } else {
          this.startX = e.clientX;
          this.startY = e.clientY;
        }
        this.moveIndex = index;
      }
    },
    handleMove(e) {
      if (this.mousedown && this.drag) {
        let moveX, moveY;
        if (this.isMobile()) {
          moveX = e.changedTouches[0].clientX;
          moveY = e.changedTouches[0].clientY;
        } else {
          moveX = e.clientX;
          moveY = e.clientY;
        }
        const x = moveX - this.startX;
        const y = moveY - this.startY;
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
        this.$nextTick(() => {
          this.mousedown = false;
          const newList = [];
          this.list.forEach((item) => {
            newList[item.index] = item;
          });
          this.list = newList;
          this.$emit("sort", newList);
        });
      }
    },
    renderList() {
      this.$nextTick(() => {
        this.list.forEach((item, index) => {
          item.index = index;
        });
      });
    },
    isMobile() {
      return navigator.userAgent.match(
        /(phone|pad|pod|iPhone|iPod|ios|iPad|Android|Mobile|BlackBerry|IEMobile|MQQBrowser|JUC|Fennec|wOSBrowser|BrowserNG|WebOS|Symbian|Windows Phone)/i
      );
    },
  },
  watch: {
    "data.length": {
      handler() {
        this.list = this.data;
        this.renderList();
      },
    },
  },
  created() {
    this.n = Math.floor(
      Number.parseInt(this.width) / Number.parseInt(this.itemWidth)
    );
    this.list = this.data;
    this.renderList();
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
