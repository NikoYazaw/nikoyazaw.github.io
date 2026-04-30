<template>
  <view class="home-container">
    <!-- 顶部背景装饰 -->
    <view class="top-bg"></view>

    <!-- 游戏标题 -->
    <view class="game-title">
      <text class="title-text">情侣飞行棋</text>
    </view>

    <!-- 模式选择区域 -->
    <view class="mode-selection">
      <view class="mode-title">
        <text>选择游戏模式</text>
      </view>

      <view class="mode-grid">
        <!-- 情侣版 -->
        <view @click="navigateToMode('qinglu')" class="mode-card">
          <view class="card-inner">
            <image src="/static/qinglu.svg" class="mode-icon"></image>
            <view class="mode-name">情侣版</view>
          </view>
        </view>

        <!-- 高级版 -->
        <view @click="navigateToMode('gaoji')" class="mode-card">
          <view class="card-inner">
            <image src="/static/gaoji.svg" class="mode-icon"></image>
            <view class="mode-name">高级版</view>
          </view>
        </view>

        <!-- 私密版 -->
        <view @click="navigateToMode('simi')" class="mode-card">
          <view class="card-inner">
            <image src="/static/simi.svg" class="mode-icon"></image>
            <view class="mode-name">私密版</view>
          </view>
        </view>

        <!-- SM版 -->
        <view @click="navigateToMode('sm')" class="mode-card">
          <view class="card-inner">
            <image src="/static/sm.svg" class="mode-icon"></image>
            <view class="mode-name">SM版</view>
          </view>
        </view>

        <!-- 女仆版 -->
        <view @click="navigateToMode('nvpu')" class="mode-card">
          <view class="card-inner">
            <image src="/static/nvpu.svg" class="mode-icon"></image>
            <view class="mode-name">女仆版</view>
          </view>
        </view>

        <!-- 丝足版 -->
        <view @click="navigateToMode('sizu')" class="mode-card">
          <view class="card-inner">
            <image src="/static/sizu.svg" class="mode-icon"></image>
            <view class="mode-name">丝足版</view>
          </view>
        </view>
      </view>
    </view>

    <!-- 游戏说明 -->
    <view class="game-description">
      <text>这是一款专为情侣设计的飞行棋游戏，包含多种趣味模式，
让你们的爱情在游戏中升温！</text>
    </view>

    <!-- 底部装饰 -->
    <view class="bottom-decor"></view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      // 可以添加一些状态数据
    };
  },
  methods: {
    // 导航到选定的游戏模式
    navigateToMode(mode) {
      uni.navigateTo({
        url: `/pages/game/${mode}`
      });
    }
  },
  onLoad() {
    // 页面加载时的逻辑
  }
};
</script>

<style scoped>
/* 基础样式 */
.home-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 40rpx 30rpx;
  background-color: #fff0f5;
  min-height: 100vh;
  position: relative;
  overflow: hidden;
}

/* 顶部背景 */
.top-bg {
  width: 100%;
  height: 300rpx;
  background: linear-gradient(135deg, #ff69b4, #ff85c0);
  position: absolute;
  top: 0;
  left: 0;
  z-index: 0;
  border-radius: 0 0 50% 50% / 20%;
}

/* 游戏标题 */
.game-title {
  margin-top: 120rpx;
  margin-bottom: 60rpx;
  position: relative;
  z-index: 1;
}

.title-text {
  font-size: 64rpx;
  font-weight: bold;
  color: white;
  text-shadow: 2rpx 2rpx 8rpx rgba(0, 0, 0, 0.2);
  letter-spacing: 4rpx;
}

/* 模式选择区域 */
.mode-selection {
  width: 90%;
  margin-bottom: 80rpx;
  position: relative;
  z-index: 1;
}

.mode-title {
  font-size: 36rpx;
  color: #ff69b4;
  margin-bottom: 40rpx;
  text-align: center;
  font-weight: bold;
}

/* 模式网格 - 改为网格布局并居中 */
.mode-grid {
  display: grid;
  grid-template-columns: repeat(2, 1fr); /* 2列布局 */
  gap: 30rpx; /* 网格间距 */
  width: 100%;
}

/* 模式按钮 */
.mode-card {
  width: 95%; /* 增加宽度，减少右边距 */
}

.card-inner {
  position: relative;
  width: 100%;
  height: 120rpx;
  text-align: center;
  background-color: white;
  border-radius: 15rpx;
  box-shadow: 0 4rpx 15rpx rgba(0, 0, 0, 0.05);
  display: flex;
  flex-direction: row;
  align-items: center;
  justify-content: center;
  padding: 0 20rpx;
  transition: all 0.3s ease;
}

.mode-card:active .card-inner {
  transform: scale(0.98);
  box-shadow: 0 2rpx 10rpx rgba(0, 0, 0, 0.08);
}

.mode-icon {
  width: 80rpx;
  height: 80rpx;
  margin-right: 20rpx;
  border-radius: 10rpx;
}

.mode-name {
  font-size: 36rpx;
  color: #333;
  font-weight: 500;
}

/* 游戏说明 */
.game-description {
  width: 85%;
  padding: 35rpx;
  background-color: rgba(255, 255, 255, 0.8);
  border-radius: 20rpx;
  text-align: center;
  margin-bottom: 100rpx;
  box-shadow: 0 4rpx 15rpx rgba(0, 0, 0, 0.05);
  position: relative;
  z-index: 1;
}

.game-description text {
  font-size: 28rpx;
  color: #666;
  line-height: 1.6;
}

/* 底部装饰 */
.bottom-decor {
  width: 100%;
  height: 120rpx;
  background: linear-gradient(135deg, #ff69b4, #ff85c0);
  position: absolute;
  bottom: 0;
  left: 0;
  z-index: 0;
  border-radius: 50% 50% 0 0 / 20%;
}
</style>