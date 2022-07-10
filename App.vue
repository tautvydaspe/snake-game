<template>
  <div id="app">
    <h1>Snake</h1>

    <div class="column">
      Cell size (px):
      <input type="number" min="10" v-model.number="cellSize" />
    </div>
    <div class="column">
      Board size (cells):
      <input type="number" min="5" v-model.number="boardSize" />
    </div>
    <div class="column">
      Speed:
      <input type="number" min="1" v-model.number="speed" />
    </div>
    <MainSnakeWindow
      :cellSize="cellSize"
      :boardSize="boardSize"
      :speed="speed"
      :isPlaying="isPlaying"
      :stop="stop"
      :addScores="addScores"
      :scores="scores"
    ></MainSnakeWindow>
    <div>Highest score: {{ scores }}</div>
    <button v-if="isPlaying" id="play-btn" @click="stop()">
      Stop
    </button>
    <button v-else id="play-btn" @click="start()">
      Play
    </button>
  </div>
</template>

<script>
import MainSnakeWindow from "./components/MainSnakeWindow";

export default {
  components: {
    MainSnakeWindow,
  },
  name: "app",
  data() {
    return {
      cellSize: 10,
      boardSize: 20,
      speed: 10,
      scores: 0,
      isPlaying: false,
    };
  },
  methods: {
    start() {
      this.isPlaying = true;
      this.scores = 0
    },
    stop() {
      this.isPlaying = false;
    },
    addScores (scores) {
      this.scores += scores
    }
  },
};
</script>

<style src="./App.css">
</style>
