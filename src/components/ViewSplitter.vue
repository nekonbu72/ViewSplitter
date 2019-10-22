<template>
  <div class="wrapper">
    <div class="item solid" @dragover="handleDragOver" v-show="isSolidShown">
      <div>{{sample}}</div>
    </div>
    <div
      class="gutter"
      draggable="true"
      @dragstart="handleDragStart"
      @dragend="handleDragEnd"
      v-show="isSolidShown"
    ></div>
    <div class="item flex" @dragover="handleDragOver">
      <div>{{sample}}</div>
    </div>
  </div>
</template>

<script>
export default {
  name: "ViewSplitter",
  data() {
    return {
      /**
       * @type {HTMLElement}
       */
      solid: {},
      before: -1,
      sample: "text ".repeat(50),
      isSolidShown: true
    };
  },
  computed: {
    minWidthSolidShown() {
      return 500;
    }
  },
  mounted() {
    this.initSolid();
    this.initSolidShown();
  },
  methods: {
    initSolid() {
      this.solid = this.$el.querySelector(".solid");
    },
    initSolidShown() {
      window.addEventListener("resize", () => {
        const wrapper = this.$el;
        this.isSolidShown = wrapper.clientWidth >= this.minWidthSolidShown;
      });
    },
    /**
     * @param {DragEvent} e
     */
    handleDragStart(e) {
      this.before = e.clientX;
      // e.preventDefault();
    },
    /**
     * @param {DragEvent} e
     */
    handleDragEnd(e) {
      const move = e.clientX - this.before;
      const paddingPx = getComputedStyle(this.solid).getPropertyValue(
        "padding"
      );
      const padding = parseInt(paddingPx);

      this.solid.style.width = `${this.solid.clientWidth - padding + move}px`;
      this.before = e.clientX;
    },
    /**
     * @param {DragEvent} e
     */
    handleDragOver(e) {
      e.preventDefault();
      e.dataTransfer.dropEffect = "move";
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.wrapper {
  display: flex;
  height: 95vh;
  /* width: 200vh; */
  border: solid 0.5vh green;
  /* resize: both;
  overflow: scroll; */
}

.item {
  padding: 2vh;
  margin: 1vh;
  overflow-y: scroll;
}

.item.solid {
  border: solid 0.5vh blue;
  width: 50vh;
  overflow-x: hidden;
  white-space: nowrap;
}

.gutter {
  width: 2vh;
  background-color: black;
  margin: 0 2vh;
  cursor: ew-resize;
}

.item.flex {
  flex: 1;
  border: solid 0.5vh red;
  overflow-x: scroll;
  white-space: nowrap;
}
</style>
