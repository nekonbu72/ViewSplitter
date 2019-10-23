<template>
  <div class="wrapper">
    <div class="item solid" @dragover="handleDragOver" v-show="isSolidShown">
      <div>{{sample}}</div>
    </div>
    <div
      class="gutter"
      draggable="true"
      @dragstart="handleDragStart"
      @drag="handleDrag"
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
      beforeClientX: -1,
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
      console.log("dragstart");
      e.dataTransfer.setData("text/plain", "");

      console.log("before:", e.clientX);
      this.beforeClientX = e.clientX;
    },

    /**
     * @param {DragEvent} e
     */
    handleDrag(e) {
      console.log("dragging", e.clientX);
    },

    /**
     * @param {DragEvent} e
     */
    handleDragEnd(e) {
      console.log("before:", this.beforeClientX, " after:", e.clientX);

      const move = e.clientX - this.beforeClientX;

      const style = window.getComputedStyle(this.solid);

      const widthPx = style.getPropertyValue("width");
      const width = parseFloat(widthPx);

      console.log("mv:", move, " st:", style, " px:", widthPx, " w:", width);

      this.solid.style.width = `${width + move}px`;
      this.beforeClientX = e.clientX;
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
