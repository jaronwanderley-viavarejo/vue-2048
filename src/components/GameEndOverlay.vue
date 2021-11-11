<template>
  <div class="overlay" v-show="show">
    <p class="message">{{ contents }}</p>
    <button class="tryAgain" @click="restart">Tentar de novo!</button>
  </div>
</template>

<script>
import { toRefs, ref, computed } from "vue";
export default {
  props: {
    board: {
      type: Object,
      required: true,
    },
    onrestart: {
      type: Function,
      required: true,
    },
  },
  setup(props) {
    const { board } = toRefs(props);
    const show = computed(() => {
      return board.value.hasWon() || board.value.hasLost();
    });
    const contents = computed(() => {
      if (board.value.hasWon()) return "Parabéns você arrasa!";
      if (board.value.hasLost()) return "Vixe... fim do jogo...";
      return "";
    });
    const restart = () => {
      props.onrestart && props.onrestart();
    }
    return {
      show,
      contents,
      restart
    }
  },
};
</script>
