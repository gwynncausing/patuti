<template>
  <div
    id="app"
    tabindex="0"
    v-on:keydown="keyDown"
    @keyup="keyUp"
    @mousemove="mousemove"
  >
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
        <!-- :class="{ 'animate-jump': jumped, dock: docked }" -->
        <div id="area"></div>
      </div>

      <ul class="bullet-right-list">
        <li class="bullet-y">
          <div
            ref="bullet3"
            class="bullet"
            :class="{ active: bullets.bullet3.isShoot }"
          ></div>
        </li>
        <li class="bullet-y">
          <div
            ref="bullet2"
            class="bullet"
            :class="{ active: bullets.bullet2.isShoot }"
          ></div>
        </li>
        <li class="bullet-y">
          <div
            ref="bullet1"
            class="bullet"
            :class="{ active: bullets.bullet1.isShoot }"
          ></div>
        </li>
      </ul>
      <!-- <div class="bullet-y"></div> -->
      <!-- <div class="bullet-x"></div> -->
    </div>
  </div>
</template>

<script>
import "@/styles/game.scss";
export default {
  name: "App",
  data() {
    return {
      mousePositionX: 0,
      mousePositionY: 0,
      defaultHeight: 161.5,
      defaultWidth: 150,
      height: 161.5,
      width: 150,
      currentImage: "idle-1.png",
      characterLocation: 0,
      jumped: false,
      docked: false,
      speed: 200,
      speedList: {
        speedWalk: 30,
        speedIdle: 200,
        speedJump: 50,
        speedJump2: 400,
        speedJump3: 600,
        speedDock: 50,
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
          isShoot: true,
          delay: Math.floor(Math.random() * 20),
          x: 0,
          y: 0,
          height: 29.5,
          width: 60.75,
          finished: false,
        },
        bullet2: {
          isShoot: false,
          delay: Math.floor(Math.random() * 20),
          x: 0,
          y: 0,
          height: 29.5,
          width: 60.75,
          finished: true,
        },
        bullet3: {
          isShoot: false,
          delay: Math.floor(Math.random() * 20),
          x: 0,
          y: 0,
          height: 29.5,
          width: 60.75,
          finished: true,
        },
      },
    };
  },
  methods: {
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
      }, 200);
    },
    getRandomNumber() {
      return Math.floor(Math.random() * 20 + 1);
    },

    bullet1Shoot() {
      setTimeout(() => {
        if (!this.bullets.bullet1.isShoot) {
          this.bullets.bullet1.isShoot = true;
          this.bullets.bullet1.delay = this.getRandomNumber();
        }
        this.bullet1Shoot();
      }, this.bullets.bullet1.delay * 1000);
    },
    bullet2Shoot() {
      setTimeout(() => {
        if (!this.bullets.bullet2.isShoot) {
          this.bullets.bullet2.isShoot = true;
          this.bullets.bullet2.delay = this.getRandomNumber();
        }
        this.bullet2Shoot();
      }, this.bullets.bullet2.delay * 1000);
    },
    bullet3Shoot() {
      setTimeout(() => {
        console.log("bullet3 called");
        if (!this.bullets.bullet3.isShoot) {
          this.bullets.bullet3.isShoot = true;
          this.bullets.bullet3.delay = this.getRandomNumber();
        }
        this.bullet3Shoot();
      }, this.bullets.bullet3.delay * 1000);
    },

    idleAction() {
      this.currentAction = this.actionList.idle;
      this.actionIteration = 0;
      // Added timeout to give time for the image to load
      setTimeout(() => {
        this.height = this.defaultHeight;
        this.width = this.defaultWidth;
      }, 200);
      this.jumped = false;
      this.speed = 200;
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

        if (bullet1Hit) {
          this.bullets.bullet1.isShoot = !this.bullets.bullet1.isShoot;
          console.log("bullet1 game over");
        }
        if (bullet2Hit) {
          this.bullets.bullet2.isShoot = !this.bullets.bullet2.isShoot;
          console.log("bullet2 game over");
        }

        if (bullet3Hit) {
          this.bullets.bullet3.isShoot = !this.bullets.bullet3.isShoot;
          console.log("bullet3 game over");
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
          // this.characterLocation -= 5;
          break;

        case "d":
        case "ArrowRight":
          if (this.characterLocation >= 174 || this.currentAction != 0) break;
          this.speed = this.speedList.speedWalk;
          this.currentAction = 2;
          // this.characterLocation += 5;
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
    mousemove(event) {
      var x = event.pageX;
      var y = event.pageY;
      this.mousePositionX = window.innerWidth - x;
      this.mousePositionY = y;
    },
  },
  created() {
    this.action();
    this.bullet1Shoot();
    this.bullet2Shoot();
    this.bullet3Shoot();
    this.timeOut200();
  },
};
</script>

<style lang="scss" scoped></style>
