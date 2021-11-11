<template>
  <div class="board" :class=[rotation] tabIndex="1">
    <div v-for="(r_item, r_i) in board.cells" :key="r_i">
      <cell v-for="(c_item, c_i) in r_item" :key="c_i"></cell>
    </div>
    <tile-view v-for="(tile, i) in tiles" :tile="tile" :key="i"> </tile-view>
    <game-end-overlay :board="board" :onrestart="onRestart"></game-end-overlay>
  </div>
</template>

<script>
import Cell from "./Cell.vue";
import TileView from "./TileView.vue";
import GameEndOverlay from "./GameEndOverlay.vue";
import { Board } from "../board";
import { onMounted, onBeforeUnmount, ref, computed } from "vue";
export default {
  setup() {
    const board = ref(new Board());
    const rotation = ref('none');
    let timeOut
    const moveToCenter = () => {
      clearTimeout(timeOut)
      timeOut = setTimeout(() => {
        rotation.value = 'none';
      }, 600);
    }

    const handleKeyDown = (event) => {
      const keys = {
        37: 'left',
        38 : 'up',
        39: 'right',
        40 : 'down' 
      }
      
      if (board.value.hasWon()) return;

      if (keys[event.keyCode]) {
        event.preventDefault();
        var direction = event.keyCode - 37;
        board.value.move(direction);
        rotation.value = keys[event.keyCode];
        moveToCenter()
      }
    };
    const onRestart = () => {
      board.value = new Board();
    };
    onMounted(() => {
      window.addEventListener("keydown", handleKeyDown);
    });
    onBeforeUnmount(() => {
      window.removeEventListener("keydown", handleKeyDown);
    });
    const tiles = computed(() => {
      return board.value.tiles.filter((tile) => tile.value != 0);
    });
    return {
      board,
      onRestart,
      tiles,
      rotation
    };
  },
  components: {
    Cell,
    TileView,
    GameEndOverlay,
  },
};
</script>

<style>
  .none {
    transform: perspective(50m);
  }
  .up {
    transform: perspective(50em) rotateX(18deg);
  }
  .down {
    transform: perspective(50em) rotateX(-18deg);
  }
  .left {
    transform: perspective(50em) rotateY(-18deg);
  }
  .right {
    transform: perspective(50em) rotateY(18deg);
  }
</style>
