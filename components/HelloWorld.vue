<template>
  <div>
    <div class="scoreTable">
      <div class="nameCont">
        <h1>Player</h1>
      </div>
      <div class="singleFrame" v-for="(frame, index) in frames" :key="index">
        <div class="strikeCont">
          <span v-if="frame.strike">X</span>
          <span v-if="frame.spare">/</span>
        </div>
        <p>{{frame.score}}</p>
        <span class="frameNo">frame {{frame.frameCount}}</span>
      </div>
      <div class="sumCont">
        <h2>{{wholeScore}}</h2>
      </div>
    </div>
    <button @click="onThrow" class="throw">Throw {{frameCount}}</button>
  </div>
</template>

<script>
// @ is an alias to /src

export default {
  name: "home",
  created() {
    // alert(this.frames[2])
  },
  data() {
    return {
      frameCount: 0,
      throwCount: 1,
      score: null,
      frame1: null,
      frame2: null,
      frames: [],
      wholeScore: 0,
      currSpare: false,
      currStrike: false,
      bonusFrame: 3
    };
  },
  methods: {
    onThrow() {
      // frame check
      if (this.frameCount < 10) {
        this.roll();
      } else if (this.frameCount < 13 && this.bonusFrame > 0) {
        if (this.currSpare || this.currStrike) {
          this.roll();
          this.bonusFrame--;
        }
      } else {
        return;
      }
    },
    roll() {
      // 1 Roll
      if (this.throwCount === 1) {
        let toStrike = 10;
        this.score = Math.floor(Math.random() * 11);

        if (this.currStrike || this.currSpare) {
          this.score += this.score;
          toStrike = 20;
        }
        // + strike
        if (this.score === toStrike || this.score === 20) {
          this.frameCount++;
          this.currStrike = true;
          this.currSpare = false;
          let frameDet = {
            score: this.score,
            spare: false,
            strike: true,
            frameCount: this.frameCount
          };
          this.throwCount--;
          this.setFrame(frameDet);
        }
        this.throwCount++;
        return;
      }
      // 2 Roll
      if (this.throwCount === 2) {
        let prevScore = this.score;
        let scoreLeft = 11 - this.score;
        if (this.currSpare || this.currStrike) {
          scoreLeft = 11 - this.score / 2;
          this.currSpare = false;
        }
        let secondRoll = Math.floor(Math.random() * scoreLeft);

        if (this.currStrike) {
          this.score += (secondRoll * 2);
          this.currStrike = false;
        } else {
          this.score += Math.floor(Math.random() * scoreLeft);
        }
        
        this.throwCount--;
        this.frameCount++;
        let frameDet = {
          score: this.score,
          spare: false,
          strike: false,
          frameCount: this.frameCount
        };
        // + spare
        if (this.score === prevScore + scoreLeft - 1 || this.score === 20) {
          this.currSpare = true;
          frameDet.spare = true;
        }
        this.setFrame(frameDet);
        return;
      }
    },
    setFrame(frame) {
      this.wholeScore += frame.score;
      this.frames.push(frame);
      this.score = 0;
    }
  }
};
</script>
<style scoped lang="scss">
.throw {
  width: 100px;
  height: 50px;
  font-size: 20px;
  position: absolute;
  top: 100px;
  left: 50%;
  transform: translateX(-50%);
}
.scoreTable {
  border: 1px solid #000;
  height: 60px;
  display: flex;
  box-sizing: border-box;
  position: relative;
  .sumCont {
    border-left: 1px solid #000;
    position: absolute;
    right: 0px;
    top: 0px;
    width: 100px;
    height: 100%;
    text-align: center;
    h2 {
      font-size: 40px;
      margin: 0px;
    }
  }
  .nameCont {
    h1 {
      margin: 0px 5px;
      line-height: 60px;
      font-size: 30px;
    }
  }
  .singleFrame {
    border-left: 1px solid #000;
    border-right: 1px solid #000;
    height: 100%;
    width: 5%;
    position: relative;
    text-align: center;
    p {
      font-size: 30px;
      line-height: 30px;
      margin: 25px 0px 0px;
    }
    .frameNo {
      position: absolute;
      left: 50%;
      transform: translateX(-50%);
      bottom: -30px;
      font-size: 12px;
    }
    .strikeCont {
      border: 1px solid #000;
      height: 20px;
      width: 20px;
      position: absolute;
      text-align: center;
      right: 0px;
      span {
        font-size: 20px;
        line-height: 20px;
      }
    }
  }
}
</style>
