<template>
  <div class="screen">
    <div
      class="screen__inner"
      :style="{
        width: `${
          ((((920 - 16 * 4) / Math.sqrt(cardsContext.length) - 16) * 3) / 4 + 16) *
          Math.sqrt(cardsContext.length)
        }px`
      }"
    >
      <card-poke
        v-for="(card, index) in cardsContext"
        :key="index"
        :ref="`card-${index}`"
        :cardsContext="cardsContext"
        :imgBackFaceUrl="`images/${card}.png`"
        :card="{ index, card: card }"
        :rules="rules"
        @onFlip="checkRule($event)"
      />
    </div>
  </div>
</template>

<script>
import CardPoke from './CardPoke.vue'
export default {
  components: { CardPoke },
  props: {
    cardsContext: {
      type: Array,
      default: function () {
        return []
      }
    }
  },
  data() {
    return {
      rules: [],
      idTimeOut: 0
    }
  },
  methods: {
    checkRule(card) {
      if (this.rules.length >= 2) return false
      this.rules.push(card)
      if (
        this.rules.length === 2 &&
        this.rules[0].card === this.rules[1].card &&
        this.rules[0].index !== this.rules[1].index
      ) {
        this.$refs[`card-${this.rules[0].index}`][0].onEnabledDisabledMode()
        this.$refs[`card-${this.rules[1].index}`][0].onEnabledDisabledMode()
        this.rules = []

        const disabledElements = document.querySelectorAll('.screen .card.disabled')
        if (disabledElements && disabledElements.length === this.cardsContext.length - 2)
          setTimeout(() => {
            this.$emit('onFinish')
          }, 920)
      } else if (this.rules.length === 2 && this.rules[0].card !== this.rules[1].card) {
        setTimeout(() => {
          this.$refs[`card-${this.rules[0].index}`][0].onFlipBackCard()
          this.$refs[`card-${this.rules[1].index}`][0].onFlipBackCard()
          this.rules = []
        }, 800)
      } else return false
    }
  }
}
</script>

<style scoped>
.screen {
  width: 100%;
  position: absolute;
  top: 0;
  left: 0;
  z-index: 2;
  background-color: var(--dark);
  color: var(--light);
}

.screen__inner {
  width: calc(424px);
  display: flex;
  flex-wrap: wrap;
  margin: 2rem auto;
}
</style>
