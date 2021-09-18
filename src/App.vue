<template>
  <div id="app" tabindex="0" v-on:keydown="key">
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
        ["dock-1.png", "dock-2.png", "dock-3.png", "dock-4.png", "dock-5.png"],
      ],
    };
  },
  methods: {
    idleAction() {
      this.currentAction = this.actionList.idle;
      this.actionIteration = 0;
      this.height = this.defaultHeight;
      this.width = this.defaultWidth;
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
            // console.log(this.jumped + " - " + this.currentImage);
            if (this.actionIteration > 3) {
              this.speed = this.speedJump2;
            }
            console.log(this.speed);
          } else this.idleAction();
        }

        // DOCK
        else if (this.currentAction === this.actionList.dock) {
          if (
            this.actionIteration <
            this.actionImagesList[this.currentAction].length - 1
          ) {
            this.actionIteration++;
          } else this.idleAction();
        }

        this.action();
      }, this.speed);
    },
    key(event) {
      let key = event.key;
      // console.log(key);
      switch (key) {
        case "a":
        case "ArrowLeft":
          console.log("left");
          if (this.characterLocation <= -40 || this.currentAction != 0) break;
          this.currentAction = 1;
          this.speed = this.speedList.speedWalk;
          console.log(this.speed);
          // this.characterLocation -= 5;
          break;

        case "d":
        case "ArrowRight":
          console.log("right");
          if (this.characterLocation >= 174 || this.currentAction != 0) break;
          this.speed = this.speedList.speedWalk;
          this.currentAction = 2;
          // this.characterLocation += 5;
          break;

        case "w":
        case "ArrowUp":
          console.log("up");
          if (this.currentAction != 0) break;
          this.currentAction = 3;
          this.speed = this.speedList.speedJump;
          this.jumped = true;
          // setTimeout(() => {
          //   this.jumped = false;
          // }, 1000);
          break;

        case "s":
        case "ArrowDown":
          console.log("down");

          if (this.currentAction != 0) break;
          this.currentAction = 4;
          break;
      }
    },
  },
  watch: {
    currentAction(newVal) {
      if (newVal == this.actionList.idle) this.jumped = false;
    },
  },
  created() {
    this.action();
  },
};
</script>

<style lang="scss" scoped></style>
