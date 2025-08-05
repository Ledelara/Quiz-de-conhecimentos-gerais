<template>
  <section class="score">
    Player <span>{{ playerScore }}</span> x <span>{{ computerScore }}</span> Computer
  </section>
</template>
<script lang="ts">
import { defineComponent } from 'vue'

export default defineComponent({
  name: 'ScoreBoard',
  props: {
    playerScore: {
      type: Number,
      default: 0
    },
    computerScore: {
      type: Number,
      default: 0
    }
  }
})
</script>
<style lang="scss">
section.score {
  border-bottom: 1px solid #000;
  padding: 24px;
  font-size: 18px;

  span {
    padding: 8px;
    font-weight: bold;
    border: 1px solid #000;
  }
}
</style>
