<template>
  <div class="blackjack">
    <h2>♠ Blackjack: Jugador vs Dealer ♣</h2>
    
    <div class="hands">
      <div>
        <h3>Jugador ({{ playerScore }})</h3>
        <p class="cards">{{ playerHand.join(' ') }}</p>
      </div>
      <div>
        <h3>Dealer ({{ showDealerScore ? dealerScore : '?' }})</h3>
        <p class="cards">
          {{ showDealerScore ? dealerHand.join(' ') : dealerHand[0] + ' ❓' }}
        </p>
      </div>
    </div>

    <div class="controls" v-if="!gameOver">
      <button @click="hit">Pedir carta</button>
      <button @click="stand">Plantarse</button>
    </div>
    <div v-else>
      <p><strong>{{ result }}</strong></p>
      <button @click="startGame">Jugar de nuevo</button>
    </div>
  </div>
</template>

<script>
export default {
  name: 'Blackjack',
  data() {
    return {
      deck: [],
      playerHand: [],
      dealerHand: [],
      gameOver: false,
      showDealerScore: false,
      result: ''
    };
  },
  computed: {
    playerScore() {
      return this.calculateScore(this.playerHand);
    },
    dealerScore() {
      return this.calculateScore(this.dealerHand);
    }
  },
  methods: {
    startGame() {
      this.deck = this.createDeck();
      this.shuffle(this.deck);
      this.playerHand = [this.deck.pop(), this.deck.pop()];
      this.dealerHand = [this.deck.pop(), this.deck.pop()];
      this.gameOver = false;
      this.showDealerScore = false;
      this.result = '';
    },
    hit() {
      this.playerHand.push(this.deck.pop());
      if (this.playerScore > 21) {
        this.endGame();
      }
    },
    stand() {
      this.showDealerScore = true;
      while (this.dealerScore < 17) {
        this.dealerHand.push(this.deck.pop());
      }
      this.endGame();
    },
    endGame() {
      this.showDealerScore = true;
      this.gameOver = true;
      const player = this.playerScore;
      const dealer = this.dealerScore;

      if (player > 21) this.result = 'Perdiste. Te pasaste.';
      else if (dealer > 21) this.result = 'Ganaste. El dealer se pasó.';
      else if (player > dealer) this.result = 'Ganaste.';
      else if (dealer > player) this.result = 'Perdiste.';
      else this.result = 'Empate.';
    },
    createDeck() {
      const suits = ['♠', '♣', '♥', '♦'];
      const values = ['A', '2', '3', '4', '5', '6', '7', '8', '9', '10', 'J', 'Q', 'K'];
      const deck = [];
      for (let suit of suits) {
        for (let value of values) {
          deck.push(value + suit);
        }
      }
      return deck;
    },
    shuffle(array) {
      for (let i = array.length - 1; i > 0; i--) {
        const j = Math.floor(Math.random() * (i + 1));
        [array[i], array[j]] = [array[j], array[i]];
      }
    },
    calculateScore(hand) {
      let score = 0;
      let aces = 0;
      for (let card of hand) {
        const value = card.slice(0, -1);
        if (value === 'A') {
          aces += 1;
          score += 11;
        } else if (['K', 'Q', 'J'].includes(value)) {
          score += 10;
        } else {
          score += parseInt(value);
        }
      }
      while (score > 21 && aces > 0) {
        score -= 10;
        aces -= 1;
      }
      return score;
    }
  },
  mounted() {
    this.startGame();
  }
};
</script>

<style scoped>
.blackjack {
  background: #000;
  color: #0f0;
  font-family: 'Courier New', Courier, monospace;
  padding: 1rem;
  border: 3px double #0f0;
  max-width: 600px;
  margin: 2rem auto;
  box-shadow: 0 0 10px #0f0;
  border-radius: 10px;
  text-align: center;
}
.cards {
  font-size: 1.5em;
  margin: 0.5rem 0;
}
button {
  margin: 0.5rem;
  padding: 0.5rem 1rem;
  background: #111;
  color: #0f0;
  border: 2px solid #0f0;
  font-weight: bold;
  cursor: pointer;
}
button:hover {
  background: #0f0;
  color: #000;
}
</style>
