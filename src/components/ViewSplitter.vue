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
      beforeClientX: -1,
      afterClientX: -1,
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
     * @param {DragEvent} event
     */
    handleDragStart(event) {
      // Firefox52 では setData しないと drag イベントが発生しない
      event.dataTransfer.setData("text/plain", "");
      this.beforeClientX = event.clientX;
    },

    handleDragEnd() {
      const moveX = this.afterClientX - this.beforeClientX;
      const style = window.getComputedStyle(this.solid);
      const widthPx = style.getPropertyValue("width");
      const width = parseFloat(widthPx);
      this.solid.style.width = `${width + moveX}px`;
      this.beforeClientX = this.afterClientX;
    },
    /**
     * @param {DragEvent} event
     */
    handleDragOver(event) {
      event.preventDefault();
      event.dataTransfer.dropEffect = "move";
      // dragend イベントでは clientX を取得できないため dragover で取得
      this.afterClientX = event.clientX;
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.wrapper {
  display: flex;
  height: 95vh;
  border: solid 0.5vh green;
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
