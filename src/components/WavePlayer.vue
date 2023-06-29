<template>
  <div class="player-container">
    <div class="stats">
      <div class="stat-detail">spawned: {{ spawnedEnemies }}</div>
      <div class="stat-detail">killed: {{ killedEnemies }}</div>
      <div class="stat-detail">on screen: {{ spawnedEnemies - killedEnemies }}</div>
    </div>
    <canvas id="canvas" image-rendering="pixelated"></canvas>
    <div class="action-container">
      <b-button class="add-button" variant="success" @click="playWave"
        >Play</b-button
      >
      <b-button class="add-button" variant="danger" @click="setStop(true)"
        >Stop</b-button
      >
      <b-form-select
        class="size-max"
        v-model="playSpeed"
        :options="[1, 2, 4, 8]"
      ></b-form-select>
    </div>
  </div>
</template>

<script>
export default {
  name: "EditableTable",
  data() {
    return {
      playSpeed: 1,
      vueCanvas: null,
      canvas: null,
      angleHashMap: new Map([
        [0, [1000, -220]],
        [15, [1428, -220]],
        [30, [1923, -220]],
        [45, [2420, 600]],
        [60, [2420, 1022]],
        [75, [2420, 1332]],
        [90, [2420, 1600]],
        [105, [2420, 1868]],
        [120, [2420, 2177]],
        [135, [2420, 2600]],
        [150, [1923, 3420]],
        [165, [1428, 3420]],
        [180, [1000, 3420]],
        [195, [571, 3420]],
        [210, [76, 3420]],
        [225, [-300, 2600]],
        [240, [-300, 2177]],
        [255, [-300, 1867]],
        [270, [-300, 1600]],
        [285, [-300, 1332]],
        [300, [-300, 1022]],
        [315, [-300, 600]],
        [330, [76, 0-220]],
        [345, [571, -220]],
        [360, [1000, -220]],
      ]),
      zombieHashMap: new Map([
        ["0", null],
        ["1", null],
        ["2", null],
        ["3", null],
        ["4", null],
        ["5", null],
        ["6", null],
        ["7", null],
        ["8", null],
        ["9", null],
        ["10", null],
        ["11", null],
        ["12", null],
        ["13", null],
        ["14", null],
        ["15", null],
      ]),
      townCenter: null,
      stackCounter: 0,
      enemyStack: [],
      numberOfEnemies: 0,
      lastStep: 0,
      canvasCenter: {
        x: 1000,
        y: 1600,
      },
      maxEnemiesOnScreen: 150,
      spawnedEnemies: 0,
      killedEnemies: 0,
      isStopped: false,
    };
  },
  components: {},
  mounted() {
    this.canvas = document.getElementById("canvas");
    this.canvas.setAttribute("height", 3200);
    this.canvas.setAttribute("width", 2000);
    let ctx = this.canvas.getContext("2d");
    ctx.imageSmoothingEnabled = false;

    this.vueCanvas = ctx;

    this.vueCanvas.fillStyle = "#80704F";
    this.vueCanvas.fillRect(0, 0, this.canvas.width, this.canvas.height);
    this.initZombieImages();
  },
  props: {
    waves: Array,
  },
  methods: {
    async playWave() {
      this.setStop(false);
      this.spawnedEnemies = 0;
      this.killedEnemies = 0;
      this.enemyStack = [];
      window.requestAnimationFrame(this.animationFrame);
      this.numberOfEnemies = this.getNumberOfEnemies();
      this.enemyStack = [];
      for (let i = 0; i < this.waves.length; i++) {
        for (let j = 0; j < this.waves[i][2]; j++) {
          while (
            this.spawnedEnemies - this.killedEnemies >=
            this.maxEnemiesOnScreen
          ) {
            if (this.isStopped) {
              this.resetWaves();
              return;
            }
            await new Promise((r) => setTimeout(r, 5));
          }
          this.addEnemyToStack(
            this.waves[i][1].toString(),
            this.angleHashMap.get(this.waves[i][0])[0],
            this.angleHashMap.get(this.waves[i][0])[1],
            this.waves[i][0]
          );
          this.spawnedEnemies += 1;
        }
      }
    },
    resetWaves() {
      // clear canvas
      this.isPaused = true;
      this.vueCanvas.clearRect(0, 0, this.canvas.width, this.canvas.height);
      this.vueCanvas.fillStyle = "#d4d6d4";
      this.vueCanvas.fillRect(0, 0, this.canvas.width, this.canvas.height);

      this.enemyStack = [];
      this.spawnedEnemies = 0;
      this.killedEnemies = 0;
    },
    setStop(c) {
      this.isStopped = c;
      this.resetWaves();
    },
    initZombieImages() {
      this.image0 = [new Image(), 142, 216];
      this.image0[0].src = require("../assets/0.png");
      this.zombieHashMap.set("0", {
        image: this.image0,
        minVelocity: 40,
        maxVelocity: 70,
      });

      this.image1 = [new Image(), 142, 216];
      this.image1[0].src = require("../assets/1.png");
      this.zombieHashMap.set("1", {
        image: this.image1,
        minVelocity: 40,
        maxVelocity: 70,
      });

      this.image2 = [new Image(), 142, 216];
      this.image2[0].src = require("../assets/2.png");
      this.zombieHashMap.set("2", {
        image: this.image2,
        minVelocity: 40,
        maxVelocity: 70,
      });

      this.image3 = [new Image(), 142, 216];
      this.image3[0].src = require("../assets/3.png");
      this.zombieHashMap.set("3", {
        image: this.image3,
        minVelocity: 40,
        maxVelocity: 70,
      });

      this.image4 = [new Image(), 142, 216];
      this.image4[0].src = require("../assets/4.png");
      this.zombieHashMap.set("4", {
        image: this.image4,
        minVelocity: 40,
        maxVelocity: 70,
      });

      this.image5 = [new Image(), 142, 216];
      this.image5[0].src = require("../assets/5.png");
      this.zombieHashMap.set("5", {
        image: this.image5,
        minVelocity: 40,
        maxVelocity: 70,
      });

      this.image6 = [new Image(), 142, 216];
      this.image6[0].src = require("../assets/6.png");
      this.zombieHashMap.set("6", {
        image: this.image6,
        minVelocity: 40,
        maxVelocity: 70,
      });

      this.image7 = [new Image(), 142, 216];
      this.image7[0].src = require("../assets/7.png");
      this.zombieHashMap.set("7", {
        image: this.image7,
        minVelocity: 40,
        maxVelocity: 70,
      });

      this.image8 = [new Image(), 142, 216];
      this.image8[0].src = require("../assets/8.png");
      this.zombieHashMap.set("8", {
        image: this.image8,
        minVelocity: 40,
        maxVelocity: 70,
      });

      this.image9 = [new Image(), 142, 216];
      this.image9[0].src = require("../assets/9.png");
      this.zombieHashMap.set("9", {
        image: this.image9,
        minVelocity: 40,
        maxVelocity: 70,
      });

      this.image10 = [new Image(), 142, 216];
      this.image10[0].src = require("../assets/10.png");
      this.zombieHashMap.set("10", {
        image: this.image10,
        minVelocity: 40,
        maxVelocity: 70,
      });

      this.image11 = [new Image(), 142, 216];
      this.image11[0].src = require("../assets/11.png");
      this.zombieHashMap.set("11", {
        image: this.image11,
        minVelocity: 40,
        maxVelocity: 70,
      });

      this.image12 = [new Image(), 142, 216];
      this.image12[0].src = require("../assets/12.png");
      this.zombieHashMap.set("12", {
        image: this.image12,
        minVelocity: 40,
        maxVelocity: 70,
      });

      this.image13 = [new Image(), 142, 216];
      this.image13[0].src = require("../assets/13.png");
      this.zombieHashMap.set("13", {
        image: this.image13,
        minVelocity: 40,
        maxVelocity: 70,
      });

      this.image14 = [new Image(), 142, 216];
      this.image14[0].src = require("../assets/14.png");
      this.zombieHashMap.set("14", {
        image: this.image14,
        minVelocity: 40,
        maxVelocity: 70,
      });

      this.image15 = [new Image(), 142, 216];
      this.image15[0].src = require("../assets/15.png");
      this.zombieHashMap.set("15", {
        image: this.image15,
        minVelocity: 40,
        maxVelocity: 70,
      });

      this.townCenter = [new Image(), 422, 429];
      this.townCenter[0].src = require("../assets/town.png");
    },
    drawZombie(zombie) {
      this.vueCanvas.drawImage(
        zombie.enemy[0],
        zombie.x,
        zombie.y,
        zombie.enemy[1] * 0.7,
        zombie.enemy[2] * 0.7
      );
    },
    drawTown() {
      this.vueCanvas.drawImage(
        this.townCenter[0],
        729,
        1351,
        this.townCenter[1] * 1.3,
        this.townCenter[2] * 1.3
      );
    },
    startDrawing: function (elapsed) {
      this.enemyStack.forEach((z) => {
        this.drawZombie(z);
        this.moveZombie(z, elapsed);
      });
    },
    moveZombie(zombie, elapsed) {
      if (this.despawnEnemyCheck(zombie)) {
        let index = this.enemyStack.indexOf(zombie);
        this.enemyStack.splice(index, 1);
        this.killedEnemies += 1;
        return;
      }
      let data = this.distanceAndAngleBetweenTwoPoints(
        zombie.x,
        zombie.y,
        this.canvasCenter.x,
        this.canvasCenter.y
      );
      let vtc = this.vectorToCenter(
        zombie.velocity * this.playSpeed * 1.3,
        data.angle
      );

      let elapsedSeconds = elapsed / 1000;

      zombie.x += vtc[0] * elapsedSeconds;
      zombie.y += vtc[1] * elapsedSeconds;
    },
    despawnEnemyCheck(zombie) {
      let distPoints =
        (this.canvasCenter.x - zombie.x) * (this.canvasCenter.x - zombie.x) +
        (this.canvasCenter.y - zombie.y) * (this.canvasCenter.y - zombie.y);
      let r = 80000;
      if (distPoints < r) {
        return true;
      }
      return false;
    },
    distanceAndAngleBetweenTwoPoints(x1, y1, x2, y2) {
      let x = x2 - x1,
        y = y2 - y1;

      return {
        distance: Math.sqrt(x * x + y * y),
        angle: (Math.atan2(y, x) * 180) / Math.PI,
      };
    },
    vectorToCenter(velocity, angle) {
      let angleRadians = (angle * Math.PI) / 180;
      let magnitudeX = velocity * Math.cos(angleRadians);
      let magnitudeY = velocity * Math.sin(angleRadians);
      return [magnitudeX, magnitudeY];
    },
    addEnemyToStack(enemy, x, y, angle) {
      let e = {
        enemy: this.zombieHashMap.get(enemy).image,
        x: x * 0.95 + Math.floor(Math.random() * (220 - -220 + 1)) - 220,
        y: y * 0.95 + Math.floor(Math.random() * (220 - -220 + 1)) + -220,
        angle: angle,
        velocity:
          Math.floor(
            Math.random() *
              (this.zombieHashMap.get(enemy).maxVelocity -
                this.zombieHashMap.get(enemy).minVelocity +
                1)
          ) + this.zombieHashMap.get(enemy).minVelocity,
      };
      this.enemyStack.push(e);
    },
    getNumberOfEnemies() {
      let zombieCount = 0;
      for (let i = 0; i < this.waves.length; i++) {
        zombieCount += this.waves[i][2];
      }
      return zombieCount;
    },
    animationFrame(milliseconds) {
      
      let elapsed = milliseconds - this.lastStep;
      this.lastStep = milliseconds;

      this.render(elapsed);
      window.requestAnimationFrame(this.animationFrame);
    },
    render(elapsed) {
      // clear canvas
      this.vueCanvas.clearRect(0, 0, this.canvas.width, this.canvas.height);
      this.vueCanvas.fillStyle = "#80704F";
      this.vueCanvas.fillRect(0, 0, this.canvas.width, this.canvas.height);
      this.drawTown();
      this.startDrawing(elapsed);
    },
  },
};
</script>

<style>
canvas {
  width: 500px;
  height: 800px;
  padding: 1rem;
  border-radius: 2rem;
}

.player-container {
  display: flex;
  width: 100%;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  padding: 2em;
  background-image: linear-gradient(#b0c2b0, #869b86);
  padding: 1rem;
  margin: 1rem;
  border-radius: 15px;
}

.action-container {
  display: flex;
}

.stats {
  display:flex;
  gap: 1rem;
  width: 100%;
  padding: 0 2rem 0 2rem;
}

.stat-detail {
  padding: 0.5rem;
  background: #d4d6d4;
  border-radius: 15px;
  width: 100%;
}
</style>
