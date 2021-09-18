<template>
  <div id="app" tabindex="0" v-on:keydown="keyDown" @keyup="keyUp">
    <div id="patuti">
      <div id="space">
        <div
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

      <!-- <ul class="bullet-right-list">
        <li class="bullet-y bullet-y-1"></li>
        <li class="bullet-y"></li>
        <li class="bullet-y"></li>
      </ul> -->

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
        speedWalk: 100,
        speedIdle: 200,
        speedJump: 200,
        speedJump2: 400,
        speedDock: 100,
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
      // 0 - idle
      // 1 left
      // 2 right
      // 3 jump
      // 4 dock
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
    };
  },
  methods: {
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
      this.intervalid1 = setTimeout(() => {
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
  },
  created() {
    this.action();
  },
};
</script>

<style lang="scss" scoped></style>
