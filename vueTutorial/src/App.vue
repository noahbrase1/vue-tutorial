<template>
  <div class="wordle-container">
    <h1>Vue Wordle</h1>

    <div class="guesses">
      <div
        v-for="(guess, guessIndex) in guesses"
        :key="guessIndex"
        class="guess-row"
      >
        <span
          v-for="(letter, letterIndex) in 5"
          :key="letterIndex"
          :class="getLetterClass(guess, letterIndex)"
          class="letter-box"
        >
          {{ guess[letterIndex] || '' }}
        </span>
      </div>
    </div>

    <input
      v-model="currentGuess"
      @keydown.enter="submitGuess"
      maxlength="5"
      :disabled="isGameOver"
      placeholder="Enter a 5 letter word"
      class="guess-input"
      aria-label="Enter your guess"
    />

    <button @click="submitGuess" :disabled="isGameOver || currentGuess.length !== 5" class="guess-button">
      Guess
    </button>

    <p v-if="message" class="message">{{ message }}</p>
    <p v-if="isGameOver && !hasWon" class="message">Game Over. The word was: {{ solution }}</p>
  </div>
</template>

<script>
export default {
  data() {
    return {
      solution: "CLAIM",
      guesses: Array(6).fill(""),
      currentGuess: "",
      currentAttempt: 0,
      hasWon: false,
      message: ""
    };
  },
  computed: {
    isGameOver() {
      return this.hasWon || this.currentAttempt >= 6;
    }
  },
  methods: {
    submitGuess() {
      const guess = this.currentGuess.toUpperCase();

      if (this.isGameOver) return;

      if (guess.length !== 5) {
        this.message = "Guess must be 5 letters long.";
        return;
      }

      // Optionally add dictionary check here

      this.guesses.splice(this.currentAttempt, 1, guess);

      if (guess === this.solution) {
        this.hasWon = true;
        this.message = "Congratulations! You guessed it!";
      } else {
        this.currentAttempt++;
        this.message = "";
      }

      this.currentGuess = "";
    },
    getLetterClass(guess, index) {
      if (!guess) return "";
      const letter = guess[index];
      const solutionLetter = this.solution[index];

      if (!letter) return "";
      if (letter === solutionLetter) return "correct";
      if (this.solution.includes(letter)) return "present";
      return "absent";
    }
  }
};
</script>

<style scoped>
.wordle-container {
  max-width: 90vw;
  margin: 2rem auto;
  text-align: center;
  font-family: Arial, sans-serif;
  padding: 1rem;
}

h1 {
  font-size: 2.5rem;
  margin-bottom: 1.5rem;
}

.guesses {
  margin-bottom: 1.5rem;
}

.guess-row {
  display: flex;
  justify-content: center;
  margin-bottom: 0.25rem;
}

.letter-box {
  width: 3.5rem;
  height: 3.5rem;
  border: 0.2rem solid #999;
  margin: 0 0.25rem;
  line-height: 3.5rem;
  font-weight: 700;
  font-size: 2rem;
  text-transform: uppercase;
  user-select: none;
  box-sizing: border-box;
  border-radius: 0.35rem;
}

.correct {
  background-color: #4caf50;
  color: white;
  border-color: #4caf50;
}

.present {
  background-color: #ffeb3b;
  color: black;
  border-color: #ffeb3b;
}

.absent {
  background-color: #9e9e9e;
  color: white;
  border-color: #9e9e9e;
}

.guess-input {
  padding: 0.5rem 1rem;
  font-size: 1.5rem;
  width: 80vw;
  max-width: 20rem;
  margin-bottom: 1rem;
  text-transform: uppercase;
  border: 0.15rem solid #999;
  border-radius: 0.3rem;
  box-sizing: border-box;
}

.guess-button {
  padding: 0.5rem 1.5rem;
  font-size: 1.25rem;
  font-weight: 700;
  cursor: pointer;
  border-radius: 0.3rem;
  border: none;
  background-color: #1976d2;
  color: white;
  transition: background-color 0.3s ease;
}

.guess-button:disabled {
  background-color: #90caf9;
  cursor: not-allowed;
}

.guess-button:not(:disabled):hover {
  background-color: #125ea7;
}

.message {
  margin-top: 1rem;
  font-weight: 700;
  color: #d32f2f;
  font-size: 1.2rem;
  min-height: 1.5rem; /* prevent layout shift */
}

/* Responsive font scaling on very small screens */
@media (max-width: 320px) {
  .letter-box {
    width: 3rem;
    height: 3rem;
    line-height: 3rem;
    font-size: 1.6rem;
  }
  .guess-input {
    font-size: 1.2rem;
    max-width: 90vw;
  }

  .guess-button {
    font-size: 1rem;
    padding: 0.4rem 1rem;
  }
}
</style>
