<template>
  <div class="game-over" @click="$emit('clicked')">
    <div class="game-over-wrapper">
      <h1>GAME OVER</h1>
      <h5>You have survived for {{ time }}</h5>
      <h3>TAP TO TRY AGAIN</h3>
    </div>
    <span class="created-by"></span>
  </div>
</template>

<script>
export default {
  name: "GameOver",
  props: {
    time: {
      type: String,
      default: "",
    },
  },
};
</script>

<style lang="scss" scoped>
.game-over {
  position: fixed;
  z-index: 9998;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-image: linear-gradient(to top, #b05ee7, #652ce2);
  cursor: pointer;

  .game-over-wrapper {
    font-family: system-ui;
    height: 100%;
    display: flex;
    justify-content: center;
    align-items: center;
    flex-direction: column;

    h1,
    h3,
    h5 {
      // font-weight: normal;
      color: #98f96b;
      -webkit-text-stroke: 2px #680568;
    }

    h1 {
      font-size: 6em;
    }

    h3 {
      font-size: 3em;
      margin: 20px;
    }

    h5 {
      font-size: 2em;
      -webkit-text-stroke: 1.5px #680568;
    }
  }

  span.created-by {
    position: absolute;
    bottom: 0;
    right: 0;
    color: whitesmoke;
    opacity: 0.5;
    margin: 5px;

    &::before {
      content: "| ";
    }

    &::after {
      content: "created by: gwynn causing";
    }
  }
}
</style>
