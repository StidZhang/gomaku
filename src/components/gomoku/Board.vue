<template>
<div class="board">
  <svg width="480" height="480" :viewBox="viewBoxInfo" ref="boardSvg">
    <g v-on:click="handleBoardClick">
      <rect :width="viewBoxSize" :height="viewBoxSize" fill="#DCB35C" />
      <rect :width="outSideRectSize" :height="outSideRectSize" x="1" y="1" stroke="#000" stroke-width="0.05" fill="none" />
      <line v-for="i in boardSize" v-bind:x1="2*i + 1" y1="1" v-bind:x2="2*i + 1" :y2="viewBoxSize-1" stroke="#000" stroke-width="0.05" fill="none"></line>
      <line v-for="i in boardSize" x1="1" v-bind:y1="2*i + 1" :x2="viewBoxSize-1" v-bind:y2="2*i + 1" stroke="#000" stroke-width="0.05" fill="none"></line>

      <g v-for="(value, i) in boardInfo" :key="i">
        <g v-if="value != 0">
          <circle v-bind:cx="getXSvgcoord(i)" v-bind:cy="getYSvgcoord(i)" r="0.9" :fill="getColor(value)">
        </g>
      </g>
      <!-- Hover over effect -->
      <g v-for="i in Array.from(Array(boardSize+1).keys())">
        <circle class="shadow-stone" v-for="j in Array.from(Array(boardSize+1).keys())" v-bind:cx="2*j + 1" v-bind:cy="2*i + 1" r="0.9"></circle>
      </g>
    </g>
  </svg>
</div>
</template>

<script>
export default {
  props: {
    'boardSize': Number,
    'boardInfo': Array
  },
  computed: {
    viewBoxSize() {
      return this.boardSize * 2
    },
    outSideRectSize() {
      return this.boardSize * 2 -2
    },
    viewBoxInfo() {
      return "0 0 " + this.viewBoxSize.toString() + " " + this.viewBoxSize.toString()
    }
  },
  methods: {
    handleBoardClick: function(evt) {
      let svg = this.$refs.boardSvg
      let pt = svg.createSVGPoint()
      pt.x = evt.clientX
      pt.y = evt.clientY
      // The cursor point, translated into svg coordinates
      var cursorpt = pt.matrixTransform(svg.getScreenCTM().inverse())

      var xSvgCoord = this.convertCoord(cursorpt.x)
      var ySvgCoord = this.convertCoord(cursorpt.y)

      // console.log("(" + xSvgCoord + ", " + ySvgCoord + ")")

      // Update the location to server
      var move = {
        x: (xSvgCoord-1) / 2,
        y: (ySvgCoord-1) / 2
      }
      this.$socket.emit("gomoku_move", move)

    },
    convertCoord(coord) {
      var lower = Math.floor(coord)
      if (lower % 2 == 0) {
        return Math.ceil(coord)
      } else {
        return lower
      }
    },
    getColor(value) {
      if (value == 1) {
        return "black"
      } else if (value == 2) {
        return "white"
      } else {
        // Error position
        return "red"
      }
    },
    getXSvgcoord(index) {
      var xCoord = index % this.boardSize
      return 2*xCoord + 1
    },
    getYSvgcoord(index) {
      var yCoord = Math.floor(index / this.boardSize)
      return 2*yCoord + 1
    }
  }
}
</script>

<style>
.shadow-stone {
  opacity: 0;
}
.shadow-stone:hover {
  opacity: 0.4;
}
</style>
