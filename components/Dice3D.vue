<template>
  <view class="dice-container">
    <view class="dice" :style="diceStyle" @click="rollDice">
      <!-- 骰子的六个面 -->
      <view class="dice-face front" :class="{ active: currentFace === 1 }">
        <view class="dot-center"></view>
      </view>
      <view class="dice-face back" :class="{ active: currentFace === 6 }">
        <view class="dot-top-left"></view>
        <view class="dot-top-right"></view>
        <view class="dot-middle-left"></view>
        <view class="dot-middle-right"></view>
        <view class="dot-bottom-left"></view>
        <view class="dot-bottom-right"></view>
      </view>
      <view class="dice-face left" :class="{ active: currentFace === 4 }">
        <view class="dot-top-left"></view>
        <view class="dot-top-right"></view>
        <view class="dot-bottom-left"></view>
        <view class="dot-bottom-right"></view>
      </view>
      <view class="dice-face right" :class="{ active: currentFace === 3 }">
        <view class="dot-top-left"></view>
        <view class="dot-center"></view>
        <view class="dot-bottom-right"></view>
      </view>
      <view class="dice-face top" :class="{ active: currentFace === 2 }">
        <view class="dot-top-left"></view>
        <view class="dot-bottom-right"></view>
      </view>
      <view class="dice-face bottom" :class="{ active: currentFace === 5 }">
        <view class="dot-top-left"></view>
        <view class="dot-top-right"></view>
        <view class="dot-center"></view>
        <view class="dot-bottom-left"></view>
        <view class="dot-bottom-right"></view>
      </view>
    </view>
    <text class="dice-text">{{statusText}}</text>
  </view>
</template>

<script>
export default {
  name: 'Dice3D',
  data() {
    return {
      currentFace: 1,
      isRolling: false,
      rollInterval: null,
      statusText: '点击骰子开始'
    };
  },
  computed: {
    diceStyle() {
      // 根据当前面计算骰子的旋转角度
      let rotateX = 0;
      let rotateY = 0;

      switch (this.currentFace) {
        case 1: // 正面
          rotateX = 0;
          rotateY = 0;
          break;
        case 2: // 顶面
          rotateX = -90;
          rotateY = 0;
          break;
        case 3: // 右面
          rotateX = 0;
          rotateY = -90;
          break;
        case 4: // 左面
          rotateX = 0;
          rotateY = 90;
          break;
        case 5: // 底面
          rotateX = 90;
          rotateY = 0;
          break;
        case 6: // 背面
          rotateX = 180;
          rotateY = 0;
          break;
      }

      return {
        transform: `rotateX(${rotateX}deg) rotateY(${rotateY}deg)`,
        transition: this.isRolling ? 'transform 0.2s' : 'transform 1s ease-out'
      };
    }
  },
  methods: {
    rollDice() {
      if (this.isRolling) return;

      this.isRolling = true;
      this.statusText = '骰子滚动中...';

      // 随机变化骰子面，模拟滚动效果
      let rollCount = 0;
      const totalRolls = 20; // 滚动次数

      this.rollInterval = setInterval(() => {
        // 随机显示一个面
        this.currentFace = Math.floor(Math.random() * 6) + 1;
        rollCount++;

        if (rollCount >= totalRolls) {
          clearInterval(this.rollInterval);
          // 最终停留的随机面
          this.currentFace = Math.floor(Math.random() * 6) + 1;
          this.isRolling = false;
          this.statusText = `点数: ${this.currentFace}`;
          // 触发自定义事件，传递骰子结果
          this.$emit('rollEnd', this.currentFace);
        }
      }, 100);
    },
    // 重置骰子状态
    reset() {
      if (this.rollInterval) {
        clearInterval(this.rollInterval);
      }
      this.isRolling = false;
      this.currentFace = 1;
      this.statusText = '点击骰子开始';
    }
  }
};
</script>

<style scoped>
.dice-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  perspective: 1000rpx;
  margin: 20rpx 0;
}

.dice {
  width: 140rpx;
  height: 140rpx;
  position: relative;
  transform-style: preserve-3d;
  transition: transform 1s;
  cursor: pointer;
  box-shadow: 0 10rpx 25rpx rgba(0, 0, 0, 0.15);
}

.dice-face {
  position: absolute;
  width: 100%;
  height: 100%;
  display: flex;
  flex-wrap: wrap;
  align-items: center;
  justify-content: center;
  background: linear-gradient(145deg, #ffffff, #f0f0f0);
  border: 4rpx solid #ff3366;
  border-radius: 15rpx;
  backface-visibility: hidden;
  padding: 15rpx;
  box-shadow: inset 0 2rpx 5rpx rgba(0, 0, 0, 0.05);
}

/* 骰子点样式 */
.dot-center,
.dot-top-left,
.dot-top-right,
.dot-middle-left,
.dot-middle-right,
.dot-bottom-left,
.dot-bottom-right {
  width: 24rpx;
  height: 24rpx;
  background-color: #e62e5c;
  border-radius: 50%;
  margin: 5rpx;
  box-shadow: inset 0 2rpx 4rpx rgba(0, 0, 0, 0.2);
}

/* 点位置 */
.dot-center {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
}

.dot-top-left {
  position: absolute;
  top: 20%;
  left: 20%;
}

.dot-top-right {
  position: absolute;
  top: 20%;
  right: 20%;
}

.dot-middle-left {
  position: absolute;
  top: 50%;
  left: 20%;
  transform: translateY(-50%);
}

.dot-middle-right {
  position: absolute;
  top: 50%;
  right: 20%;
  transform: translateY(-50%);
}

.dot-bottom-left {
  position: absolute;
  bottom: 20%;
  left: 20%;
}

.dot-bottom-right {
  position: absolute;
  bottom: 20%;
  right: 20%;
}

/* 骰子各面位置 */
.front {
  transform: translateZ(70rpx);
}

.back {
  transform: rotateY(180deg) translateZ(70rpx);
}

.left {
  transform: rotateY(-90deg) translateZ(70rpx);
}

.right {
  transform: rotateY(90deg) translateZ(70rpx);
}

.top {
  transform: rotateX(90deg) translateZ(70rpx);
}

.bottom {
  transform: rotateX(-90deg) translateZ(70rpx);
}

.dice-text {
  font-size: 24rpx;
  color: #ffcc00;
  margin-top: 60rpx;
  font-weight: normal;
  text-shadow: 0 2rpx 4rpx rgba(0, 0, 0, 0.3);
}
</style>