<template>
  <div id="app" tabindex="0" v-on:keydown="keyDown" @keyup="keyUp">
    <div id="patuti">
      <div id="space">
        <div
          ref="character"
          id="character"
          :style="{
            left: characterLocation + 'px',
            'background-image': `url(${require('@/assets/' + currentImage)})`,
            height: height + 'px',
            width: width + 'px',
          }"
          :class="{ 'animate-jump': jumped }"
        ></div>
        <div id="area"></div>
      </div>

      <ul class="bullet-right-list">
        <li class="bullet-x">
          <div
            ref="bullet3"
            class="bullet"
            :class="{ active: bullets.bullet3.isShoot }"
          ></div>
        </li>
        <li class="bullet-x">
          <div
            ref="bullet2"
            class="bullet"
            :class="{ active: bullets.bullet2.isShoot }"
          ></div>
        </li>
        <li class="bullet-x">
          <div
            ref="bullet1"
            class="bullet"
            :class="{ active: bullets.bullet1.isShoot }"
          ></div>
        </li>
      </ul>

      <ul class="bullet-top-list">
        <li class="bullet-y">
          <div
            ref="bullet6"
            class="bullet"
            :class="{ active: bullets.bullet6.isShoot }"
          ></div>
        </li>
        <li class="bullet-y">
          <div
            ref="bullet5"
            class="bullet"
            :class="{ active: bullets.bullet5.isShoot }"
          ></div>
        </li>
        <li class="bullet-y">
          <div
            ref="bullet4"
            class="bullet"
            :class="{ active: bullets.bullet4.isShoot }"
          ></div>
        </li>
      </ul>
    </div>

    <div class="page-bg"></div>

    <GameOver
      v-if="showModal"
      :time="msToTime()"
      @clicked="showModal = false"
    />

    <!-- <Modal v-if="showModal" @close="showModal = false">
      <div slot="header">
        <h3>You Dead!</h3>
      </div>
      <div slot="body">
        <h3>You have survived for {{ msToTime() }}</h3>
      </div>
      <div slot="footer">
        <button @click="showModal = false">Restart</button>
      </div>
    </Modal> -->

    <button v-if="!play" class="play" @click="playGame()">Play</button>

    <div class="life-wrapper">
      <table class="life-bar-wrapper">
        <td class="life-bar"></td>
        <td class="life-bar"></td>
        <td class="life-bar"></td>
        <td class="life-bar"></td>
        <td class="life-bar"></td>
        <td class="life-bar"></td>
        <td class="life-bar"></td>
        <td class="life-bar"></td>
        <td class="life-bar"></td>
        <td class="life-bar"></td>
      </table>
      <div class="life" :style="{ width: life * 10 + '%' }"></div>
    </div>
  </div>
</template>

<script>
import "@/styles/game.scss";
import GameOver from "@/components/GameOver.vue";
// import Modal from "@/components/Modal.vue";

export default {
  name: "App",
  components: {
    GameOver,
  },
  data() {
    return {
      play: false,
      showModal: false,
      life: 10,
      window: {},
      defaultHeight: 161.5,
      defaultWidth: 150,
      height: 161.5,
      width: 150,
      currentImage: "idle-1.png",
      characterLocation: 0,
      jumped: false,
      docked: false,
      speed: 100,
      speedList: {
        speedWalk: 10,
        speedIdle: 100,
        speedJump: 50,
        speedJump2: 400,
        speedJump3: 50,
        speedDock: 30,
      },
      walkPixels: 10,
      currentAction: 0,
      actionList: {
        idle: 0,
        left: 1,
        right: 2,
        jump: 3,
        dock: 4,
      },
      actionIteration: 0,
      actionImagesList: [
        ["idle-1.png", "idle-2.png"],
        ["left-1.png", "left-2.png", "left-3.png", "left-4.png", "left-5.png"],
        [
          "right-1.png",
          "right-2.png",
          "right-3.png",
          "right-4.png",
          "right-5.png",
        ],
        [
          {
            name: "jump-1.png",
            height: 161.5,
            width: 150,
          },
          {
            name: "jump-2.png",
            height: 157,
            width: 154,
          },
          {
            name: "jump-3.png",
            height: 150.5,
            width: 150,
          },
          {
            name: "jump-4.png",
            height: 178,
            width: 128,
          },
          {
            name: "jump-5.png",
            height: 178,
            width: 128,
          },
          {
            name: "jump-6.png",
            height: 157,
            width: 154,
          },
          {
            name: "jump-7.png",
            height: 150.5,
            width: 150,
          },
        ],
        [
          {
            name: "dock-1.png",
            width: 150,
            height: 161.5,
          },
          {
            name: "dock-2.png",
            width: 150,
            height: 149.5,
          },
          {
            name: "dock-3.png",
            width: 150,
            height: 135,
          },
          {
            name: "dock-4.png",
            width: 136,
            height: 94.5,
          },
          {
            name: "dock-5.png",
            width: 134,
            height: 120,
          },
        ],
      ],
      bullets: {
        bullet1: {
          isShoot: false,
          delay: Math.floor(Math.random() * 20),
          x: 0,
          y: 0,
          height: 29.5,
          width: 60.75,
          finished: true,
        },
        bullet2: {
          isShoot: false,
          delay: Math.floor(Math.random() * 20),
          x: 0,
          y: 0,
          height: 29.5,
          width: 60.75,
        },
        bullet3: {
          isShoot: false,
          delay: Math.floor(Math.random() * 20),
          x: 0,
          y: 0,
          height: 29.5,
          width: 60.75,
        },
        bullet4: {
          isShoot: false,
          delay: Math.floor(Math.random() * 20),
          x: 0,
          y: 0,
          height: 60.75,
          width: 29.5,
        },
        bullet5: {
          isShoot: false,
          delay: Math.floor(Math.random() * 20),
          x: 0,
          y: 0,
          height: 60.75,
          width: 29.5,
        },
        bullet6: {
          isShoot: false,
          delay: Math.floor(Math.random() * 20),
          x: 0,
          y: 0,
          height: 60.75,
          width: 29.5,
        },
      },
      time: {
        timeStart: 0,
        timeEnd: 0,
      },
    };
  },
  methods: {
    msToTime() {
      let duration = this.time.timeEnd - this.time.timeStart;
      // milliseconds = parseInt((duration % 1000) / 100)
      var seconds = Math.floor((duration / 1000) % 60),
        minutes = Math.floor((duration / (1000 * 60)) % 60),
        hours = Math.floor((duration / (1000 * 60 * 60)) % 24);

      hours = hours < 10 ? "0" + hours : hours;
      minutes = minutes < 10 ? "0" + minutes : minutes;
      seconds = seconds < 10 ? "0" + seconds : seconds;

      return hours + ":" + minutes + ":" + seconds;
    },
    getTime() {
      let date = new Date();
      let time = date.getTime();
      return time;
    },
    playGame() {
      this.life = 10;
      this.showModal = false;
      this.play = true;
      this.bullet1Shoot();
      this.bullet2Shoot();
      this.bullet3Shoot();
      this.bullet4Shoot();
      this.bullet5Shoot();
      this.bullet6Shoot();
      this.time.timeStart = this.getTime();
    },
    gameOver() {
      this.time.timeEnd = this.getTime();
      this.showModal = true;
      this.play = false;
      this.life = 10;
    },
    lifeUpdate() {
      this.life = this.life - 1;
      if (this.life === 0) {
        this.gameOver();
      }
    },
    timeOut200() {
      setInterval(() => {
        if (this.bullets.bullet1.x < 20) {
          this.bullets.bullet1.isShoot = false;
        }
        if (this.bullets.bullet2.x < 20) {
          this.bullets.bullet2.isShoot = false;
        }
        if (this.bullets.bullet3.x < 20) {
          this.bullets.bullet3.isShoot = false;
        }
        if (this.bullets.bullet4.y > this.window.height - 100) {
          this.bullets.bullet4.isShoot = false;
        }
        if (this.bullets.bullet5.y > this.window.height - 100) {
          this.bullets.bullet5.isShoot = false;
        }
        if (this.bullets.bullet6.y > this.window.height - 100) {
          this.bullets.bullet6.isShoot = false;
        }
      }, 200);
    },
    getRandomNumber() {
      return Math.floor(Math.random() * 20 + 1);
    },

    bullet1Shoot() {
      setTimeout(() => {
        if (!this.play) return;
        if (!this.bullets.bullet1.isShoot) {
          this.bullets.bullet1.isShoot = true;
          this.bullets.bullet1.delay = this.getRandomNumber();
        }
        this.bullet1Shoot();
      }, this.bullets.bullet1.delay * 1000);
    },
    bullet2Shoot() {
      setTimeout(() => {
        if (!this.play) return;
        if (!this.bullets.bullet2.isShoot) {
          this.bullets.bullet2.isShoot = true;
          this.bullets.bullet2.delay = this.getRandomNumber();
        }
        this.bullet2Shoot();
      }, this.bullets.bullet2.delay * 1000);
    },
    bullet3Shoot() {
      setTimeout(() => {
        if (!this.play) return;
        if (!this.bullets.bullet3.isShoot) {
          this.bullets.bullet3.isShoot = true;
          this.bullets.bullet3.delay = this.getRandomNumber();
        }
        this.bullet3Shoot();
      }, this.bullets.bullet3.delay * 1000);
    },
    bullet4Shoot() {
      setTimeout(() => {
        if (!this.play) return;
        if (!this.bullets.bullet4.isShoot) {
          this.bullets.bullet4.isShoot = true;
          this.bullets.bullet4.delay = this.getRandomNumber();
        }
        this.bullet4Shoot();
      }, this.bullets.bullet4.delay * 1000);
    },
    bullet5Shoot() {
      setTimeout(() => {
        if (!this.play) return;
        if (!this.bullets.bullet5.isShoot) {
          this.bullets.bullet5.isShoot = true;
          this.bullets.bullet5.delay = this.getRandomNumber();
        }
        this.bullet5Shoot();
      }, this.bullets.bullet5.delay * 1000);
    },
    bullet6Shoot() {
      setTimeout(() => {
        if (!this.play) return;
        if (!this.bullets.bullet6.isShoot) {
          this.bullets.bullet6.isShoot = true;
          this.bullets.bullet6.delay = this.getRandomNumber();
        }
        this.bullet6Shoot();
      }, this.bullets.bullet6.delay * 1000);
    },

    idleAction() {
      this.currentAction = this.actionList.idle;
      this.actionIteration = 0;
      // Added timeout to give time for the image to load
      setTimeout(() => {
        this.height = this.defaultHeight;
        this.width = this.defaultWidth;
      }, this.speedList.speedIdle);
      this.jumped = false;
      this.speed = this.speedList.speedIdle;
    },
    action: function () {
      setTimeout(() => {
        let act =
          this.actionImagesList[this.currentAction][this.actionIteration];

        if (this.currentAction <= this.actionList.right)
          this.currentImage = act;
        else {
          this.currentImage = act.name;
          this.height = act.height;
          this.width = act.width;
        }

        // IDLE
        if (this.currentAction === this.actionList.idle) {
          this.actionIteration === 1
            ? (this.actionIteration = 0)
            : (this.actionIteration = 1);
        }

        // LEFT
        else if (this.currentAction === this.actionList.left) {
          if (
            this.actionIteration <
            this.actionImagesList[this.currentAction].length - 1
          ) {
            this.actionIteration++;
            this.characterLocation -= this.walkPixels;
          } else this.idleAction();
        }

        // RIGHT
        else if (this.currentAction === this.actionList.right) {
          if (
            this.actionIteration <
            this.actionImagesList[this.currentAction].length - 1
          ) {
            this.actionIteration++;
            this.characterLocation += this.walkPixels;
          } else this.idleAction();
        }

        // JUMP
        else if (this.currentAction === this.actionList.jump) {
          if (this.actionIteration === 3)
            this.speed = this.speedList.speedJump2;
          if (this.actionIteration === 6)
            this.speed = this.speedList.speedJump3;
          if (
            this.actionIteration <
            this.actionImagesList[this.currentAction].length - 1
          ) {
            this.actionIteration++;
          } else this.idleAction();
        }

        // DOCK
        else if (this.currentAction === this.actionList.dock) {
          if (this.docked) {
            if (
              this.actionIteration <
              this.actionImagesList[this.currentAction].length - 1
            ) {
              if (this.actionIteration < 3) {
                this.actionIteration++;
              }
            }
          } else if (!this.docked && this.actionIteration == 3) {
            this.actionIteration++;
          } else {
            this.idleAction();
          }
        }

        let character = {
          width: this.$refs.character.clientWidth,
          height: this.$refs.character.clientHeight,
          x: this.$refs.character.getBoundingClientRect().left,
          y: this.$refs.character.getBoundingClientRect().top,
        };

        this.bullets.bullet1.x =
          this.$refs.bullet1.getBoundingClientRect().left;
        this.bullets.bullet1.y = this.$refs.bullet1.getBoundingClientRect().top;

        this.bullets.bullet2.x =
          this.$refs.bullet2.getBoundingClientRect().left;
        this.bullets.bullet2.y = this.$refs.bullet2.getBoundingClientRect().top;

        this.bullets.bullet3.x =
          this.$refs.bullet3.getBoundingClientRect().left;
        this.bullets.bullet3.y = this.$refs.bullet3.getBoundingClientRect().top;

        this.bullets.bullet4.x =
          this.$refs.bullet4.getBoundingClientRect().left;
        this.bullets.bullet4.y = this.$refs.bullet4.getBoundingClientRect().top;

        this.bullets.bullet5.x =
          this.$refs.bullet5.getBoundingClientRect().left;
        this.bullets.bullet5.y = this.$refs.bullet5.getBoundingClientRect().top;

        this.bullets.bullet6.x =
          this.$refs.bullet6.getBoundingClientRect().left;
        this.bullets.bullet6.y = this.$refs.bullet6.getBoundingClientRect().top;

        let bullet1 = this.bullets.bullet1;
        let bullet1Hit =
          character.x < bullet1.x + bullet1.width &&
          character.x + character.width > bullet1.x &&
          character.y < bullet1.y + bullet1.height &&
          character.y + character.height > bullet1.y;

        let bullet2 = this.bullets.bullet2;
        let bullet2Hit =
          character.x < bullet2.x + bullet2.width &&
          character.x + character.width > bullet2.x &&
          character.y < bullet2.y + bullet2.height &&
          character.y + character.height > bullet2.y;

        let bullet3 = this.bullets.bullet3;
        let bullet3Hit =
          character.x < bullet3.x + bullet3.width &&
          character.x + character.width > bullet3.x &&
          character.y < bullet3.y + bullet3.height &&
          character.y + character.height > bullet3.y;

        let bullet4 = this.bullets.bullet4;
        let bullet4Hit =
          character.x < bullet4.x + bullet4.width &&
          character.x + character.width > bullet4.x &&
          character.y < bullet4.y + bullet4.height &&
          character.y + character.height > bullet4.y;

        let bullet5 = this.bullets.bullet5;
        let bullet5Hit =
          character.x < bullet5.x + bullet5.width &&
          character.x + character.width > bullet5.x &&
          character.y < bullet5.y + bullet5.height &&
          character.y + character.height > bullet5.y;

        let bullet6 = this.bullets.bullet6;
        let bullet6Hit =
          character.x < bullet6.x + bullet6.width &&
          character.x + character.width > bullet6.x &&
          character.y < bullet6.y + bullet6.height &&
          character.y + character.height > bullet6.y;

        if (bullet1Hit) {
          this.bullets.bullet1.isShoot = false;
          this.lifeUpdate();
        }
        if (bullet2Hit) {
          this.bullets.bullet2.isShoot = false;
          this.lifeUpdate();
        }

        if (bullet3Hit) {
          this.bullets.bullet3.isShoot = false;
          this.lifeUpdate();
        }

        if (bullet4Hit) {
          this.bullets.bullet4.isShoot = false;
          this.lifeUpdate();
        }

        if (bullet5Hit) {
          this.bullets.bullet5.isShoot = false;
          this.lifeUpdate();
        }

        if (bullet6Hit) {
          this.bullets.bullet6.isShoot = false;
          this.lifeUpdate();
        }

        this.action();
      }, this.speed);
    },
    keyDown(event) {
      let key = event.key;
      switch (key) {
        case "a":
        case "ArrowLeft":
          if (this.characterLocation <= -40 || this.currentAction != 0) break;
          this.currentAction = 1;
          this.speed = this.speedList.speedWalk;
          break;

        case "d":
        case "ArrowRight":
          if (this.characterLocation >= 174 || this.currentAction != 0) break;
          this.speed = this.speedList.speedWalk;
          this.currentAction = 2;
          break;

        case "w":
        case "ArrowUp":
          if (this.currentAction != 0) break;
          this.currentAction = 3;
          this.speed = this.speedList.speedJump;
          this.jumped = true;
          break;

        case "s":
        case "ArrowDown":
          if (this.currentAction != 0) break;
          this.currentAction = 4;
          this.docked = true;
          this.speed = this.speedList.speedDock;
          break;
      }
    },
    keyUp(event) {
      switch (event.key) {
        case "s":
        case "ArrowDown":
          this.docked = false;
          break;
      }
    },
  },
  created() {
    this.action();
    this.timeOut200();
    this.characterLocation = this.getRandomNumber() * 10;
    this.window = {
      height: window.innerHeight,
      width: window.innerWidth,
    };
  },
};
</script>

<style lang="scss" scoped></style>
