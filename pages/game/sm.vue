<template>
  <view class="game-container">
    <!-- 启动弹窗 -->
    <view class="start-modal-mask" v-if="showStartModal"></view>
    <view class="start-modal-container" v-if="showStartModal">
      <view class="start-modal-content">
        <view class="start-modal-title">SM版（仅限于18岁以上使用）</view>
        <view class="start-modal-text">
          <p>游戏规则：按游戏玩法中的准备建议的道具，也可以自己修改奖励或惩罚。走棋规则，默认男生先行，走到对应的格子为对方做惩罚，福利自己享受，无法接受内容可自由协商做别的惩罚。</p>
          <p><text @click="navigateToRules" class="link">游戏玩法</text></p>
        </view>
        <button class="start-modal-btn" @click="handleAgree">已知悉</button>
      </view>
    </view>
    <!-- 弹窗组件 -->
    <view class="modal-mask" v-if="showModal"></view>
    <view class="modal-container" v-if="showModal">
      <view class="modal-content">
        <view class="modal-title">{{currentPlayer === 'boy' ? '男生' : '女生'}}任务</view>
        <view class="modal-text">{{currentModalContent}}</view>
        <button class="modal-btn" @click="handleComplete">已完成</button>
      </view>
    </view>
    <!-- 模式选择 -->
    <view class="mode-selector">
      <button @click="switchMode('qinglu')" :class="currentMode === 'qinglu' ? 'active' : ''">情侣版</button>
      <button @click="switchMode('gaoji')" :class="currentMode === 'gaoji' ? 'active' : ''">高级版</button>
      <button @click="switchMode('simi')" :class="currentMode === 'simi' ? 'active' : ''">私密版</button>
      <button @click="switchMode('sm')" :class="currentMode === 'sm' ? 'active' : ''">SM版</button>
      <button @click="switchMode('nvpu')" :class="currentMode === 'nvpu' ? 'active' : ''">女仆版</button>
      <button @click="switchMode('sizu')" :class="currentMode === 'sizu' ? 'active' : ''">丝足版</button>
    </view>
    
    <!-- 棋盘区域 -->
    <view class="board-container">
      <view class="game-board">
        <!-- 棋盘格子 -->
        <view v-for="(cell, index) in boardCells" :key="index" :class="['cell', cell.type]">
          <text v-if="cell.number">{{cell.number}}</text>
          <image v-if="cell.icon" :src="cell.icon" class="cell-icon"></image>
          
          <!-- 棋子位置 -->
          <view v-if="cell.boyPosition" class="chess-container">
            <image src="https://cloud.2fk.cn/qlfxq/nan.png" class="chess-icon"></image>
          </view>
          <view v-if="cell.girlPosition" class="chess-container">
            <image src="https://cloud.2fk.cn/qlfxq/nv.png" class="chess-icon"></image>
          </view>
        </view>
      </view>
    </view>
    
    <!-- 骰子区域 -->
    <view class="dice-container">
      <Dice3D ref="dice" @roll-end="handleRollEnd" />
      <text class="dice-text">{{currentPlayer === 'boy' ? '男生' : '女生'}}回合</text>
    </view>
    
    <!-- 游戏控制区域 -->
    <view class="control-container">
      <button class="btn" @click="restartGame">重新开始</button>
      <button class="btn back" @click="goBack">返回主页</button>
    </view>
  </view>
</template>

<script>
import Dice3D from '@/components/Dice3D.vue'
export default {
  components: {
    Dice3D
  },
  data() {
    return {
      boardCells: [], // 棋盘格子数据
      diceValue: 1, // 骰子值
      isRolling: false, // 是否正在掷骰子
      currentPlayer: 'boy', // 当前玩家，'boy'或'girl'
      boyPosition: 0, // 男生位置
      girlPosition: 0, // 女生位置
      gameStarted: false, // 游戏是否开始
      currentMode: 'sm', // 当前模式
      boardSize: 62, // 棋盘大小 (0-61共62个格子)
      showModal: false, // 是否显示弹窗
      currentModalContent: '', // 当前弹窗内容
      isSpecialPosition: false, // 是否为特殊位置
      targetPosition: 0, // 特殊位置对应的目标位置
      showStartModal: true, // 是否显示启动弹窗
      gameEnabled: false // 游戏是否启用
    };
  },
  onLoad() {
    this.initBoard();
  },
  methods: {
    // 初始化棋盘
    initBoard() {
      // 创建60个格子
      const cells = [];
      for (let i = 0; i < this.boardSize; i++) {
        const cell = {
          number: i,
          type: this.getCellType(i),
          icon: null,
          boyPosition: false,
          girlPosition: false
        };
        cells.push(cell);
      }
      
      // 设置起点(0)和终点(61)
      cells[0].type = 'start';
      cells[0].number = '起点'; // 显示起点文字
      cells[61].type = 'end';
      cells[61].number = '终点'; // 显示终点文字
      
      // 设置特殊格子 (不使用图标)
      this.setSpecialCells(cells);
      
      // 设置SM版路线 (之字形路线)
      this.setSMPath(cells);
      
      this.boardCells = cells;
      this.boyPosition = 0;
      this.girlPosition = 0;
      this.updateChessPositions();
    },
    
    // 设置SM版路线 (之字形)
    setSMPath(cells) {
      // SM版采用之字形路线
      // 这里可以设置格子的视觉排列顺序
    },
    
    // 获取格子类型
    getCellType(index) {
      // 普通格子
      let type = 'normal';
      
      // 起点和终点
      if (index === 0) return 'start';
      if (index === this.boardSize - 1) return 'end';
      
      // 每隔6个格子设置为特殊格子
      if (index % 6 === 0) {
        type = 'special';
      }
      
      return type;
    },
    
    // 设置特殊格子
    setSpecialCells(cells) {
      // 在一些位置设置特殊格子 (不使用图标)
      // 调整位置以适应0-60的中间格子
      const specialPositions = [5, 11, 17, 23, 29, 35, 41, 47, 53, 59];
      specialPositions.forEach(pos => {
        if (pos < cells.length) {
          // 这里可以设置不同类型的特殊格子
          cells[pos].icon = null; // 不使用图标
        }
      });
    },
    
    // 处理骰子滚动结束事件
    handleRollEnd(value) {
      this.diceValue = value;
      this.moveChess();
    },

    // 移动棋子
    moveChess() {
      if (!this.gameEnabled) return; // 游戏未启用时不能移动棋子

      const steps = this.diceValue;

      if (this.currentPlayer === 'boy') {
        this.boyPosition = Math.min(this.boyPosition + steps, 61); // 最大位置为61
        this.showModalContent(this.boyPosition);
      } else {
        this.girlPosition = Math.min(this.girlPosition + steps, 61); // 最大位置为61
        this.showModalContent(this.girlPosition);
      }

      // 更新棋子位置
      this.updateChessPositions();

      // 检查是否获胜
      this.checkWin();
    },

    // 处理已知悉按钮点击
    handleAgree() {
      this.showStartModal = false;
      this.gameEnabled = true;
    },

    // 跳转到游戏玩法页面
    navigateToRules() {
      uni.navigateTo({ url: '/pages/game/sm_rules' });
    },
    
    // 根据位置显示弹窗内容
    showModalContent(position) {
      // 定义各个位置对应的内容
      const positionContents = {
        1: '让对方穿上内裤后舔湿',
        2: '嘴里含着酸奶流出让对方舔干净',
        3: '戴上眼罩任由对方处置30秒',
        4: '按住对方头给自己舔指定部位30秒',
        5: '涂上润滑油给对方乳交1分钟',
        6: '回到起点',
        7: '吸吮对方脚趾30秒',
        8: '牵狗绳到房间门口口交1分钟',
        9: '舔对方脖子到胸口30秒',
        10: '用按摩棒（跳蛋）按摩到流水或勃起',
        11: '戴上项圈到房间门口口交1分钟',
        12: '戴上眼罩牵狗绳快速抽插到高潮并内射',
        13: '拴上十字扣抽插一分钟不许射',
        14: '手脚拷上让对方摧残30秒',
        15: '用嘴含住对方蛋蛋或阴蒂30秒',
        16: '给对方戴上口球挑逗1分钟',
        17: '坐到对方脸上让对方吮吸',
        18: '回到15',
        19: '让对方含着口交水口交30秒',
        20: '给对方下体倒酸奶并舔干净',
        21: '跪着张开嘴让对方下体磨蹭30秒',
        22: '穿上丝袜跪下让对方撕开并抽插30秒',
        23: '用B或jj蹭对方脸30秒',
        24: '回到20',
        25: '被对方用皮鞭抽胸5次',
        26: '牵狗绳插入一分钟并用皮鞭抽',
        27: '牵狗绳拉到卫生间淋湿',
        28: '吸吮对方乳头10秒',
        29: '给对方戴上眼罩任由自己宰割一分钟',
        30: '【免做卡】获得免做卡，可任意拒绝一次',
        31: '按在地板上抽插15秒',
        32: '给对方乳交30秒并拍视频',
        33: '戴上眼罩享受对方舔遍全身',
        34: '胸夹铃铛扣对方20秒',
        35: '女方撅起pp让对方吸B20秒',
        36: '直达终点',
        37: '观音坐莲皮鞭抽胸1分钟',
        38: '戴上眼罩任由对方处置30秒',
        39: '被对方使用3钟道具1分钟',
        40: '为对方口交被强行按住头20秒',
        41: '绑在凳子上皮鞭抽1分钟',
        42: '回到34',
        43: '女方撅起pp求草被后入30秒皮鞭抽pp',
        44: '拍一段对方戴眼罩被你摧残的视频',
        45: '【福利】躺下享受被对方舔遍全身',
        46: '摆一个诱惑的姿势被对方拍照',
        47: '同时舔对方胸并抚摸下体',
        48: '直达50',
        49: '挑逗对方敏感部位直到对方求饶',
        50: '想办法让对方勃起或流水',
        51: '翘起pp让任意让对方任意抽打20秒',
        52: '将对方的pp舔一遍',
        53: '分开双腿让对方仔细观察下体30秒',
        54: '把所有道具在对方身上用一遍',
        55: '用嘴含住对方蛋蛋或阴蒂30秒',
        56: '被对方使用3钟道具1分钟',
        57: '把酒倒在自己身上对方舔干净',
        58: '戴上眼罩享受对方舔遍全身',
        59: '戴上眼罩牵狗绳快速抽插到高潮并内射',
        60: '回到起点',
        61: '获胜方请指定自己喜欢的体位尽情享受做爱的快乐~'
      };
      
      // 特殊跳转位置
      const specialJumps = {
        6: 0,     // 回到起点
        18: 15,   // 回到15
        24: 20,   // 回到20
        36: 61,   // 直达终点
        42: 34,   // 回到34
        48: 50,   // 直达50
        60: 0     // 回到起点
      };
      
      // 检查是否是特殊跳转位置
      if (specialJumps[position] !== undefined) {
        this.isSpecialPosition = true;
        this.targetPosition = specialJumps[position];
      } else {
        this.isSpecialPosition = false;
      }
      
      // 设置弹窗内容并显示
      if (positionContents[position]) {
        this.currentModalContent = positionContents[position];
        this.showModal = true;
      }
    },
    
    // 处理已完成按钮点击
    handleComplete() {
      this.showModal = false;
      
      // 如果是特殊位置，执行跳转
      if (this.isSpecialPosition) {
        if (this.currentPlayer === 'boy') {
          this.boyPosition = this.targetPosition;
        } else {
          this.girlPosition = this.targetPosition;
        }
        this.updateChessPositions();
        this.checkWin();
        this.isSpecialPosition = false;
      }
      
      // 切换玩家
      this.currentPlayer = this.currentPlayer === 'boy' ? 'girl' : 'boy';
    },
    
    // 更新棋子位置
    updateChessPositions() {
      // 重置所有棋子位置
      this.boardCells.forEach(cell => {
        cell.boyPosition = false;
        cell.girlPosition = false;
      });
      
      // 设置当前位置
      if (this.boyPosition < this.boardCells.length) {
        this.boardCells[this.boyPosition].boyPosition = true;
      }
      
      if (this.girlPosition < this.boardCells.length) {
        this.boardCells[this.girlPosition].girlPosition = true;
      }
    },
    
    // 检查是否获胜
    checkWin() {
      if (this.boyPosition === 61) { // 终点位置为61
        uni.showToast({
          title: '男生获胜！请指定自己喜欢的体位尽情享受做爱的快乐~',
          icon: 'none'
        });
        this.gameStarted = false;
      } else if (this.girlPosition === 61) { // 终点位置为61
        uni.showToast({
          title: '女生获胜！请指定自己喜欢的体位尽情享受做爱的快乐~',
          icon: 'none'
        });
        this.gameStarted = false;
      }
    },
    
    // 重新开始游戏
    restartGame() {
      this.initBoard();
      this.currentPlayer = 'boy';
      this.gameStarted = true;
      this.resetDice();
    },

    // 重置骰子
    resetDice() {
      if (this.$refs.dice) {
        this.$refs.dice.reset();
      }
    },
    
    // 切换模式
    switchMode(mode) {
      if (mode === this.currentMode) return;
      
      // 跳转到对应的模式页面
      uni.navigateTo({
        url: `/pages/game/${mode}`
      });
    },
    
    // 返回主页
    goBack() {
      uni.navigateTo({
        url: '/pages/index/index'
      });
    }
  }
};
</script>

<style scoped>
.game-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 20rpx;
  background-color: #1a102c;
  min-height: 100vh;
}

.game-title {
  font-size: 48rpx;
  font-weight: bold;
  margin: 30rpx 0;
  color: #ff3366;
}

.mode-selector {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  margin-bottom: 20rpx;
  width: 90%;
}

.mode-selector button {
  margin: 10rpx;
  padding: 10rpx 20rpx;
  font-size: 24rpx;
  background-color: #333333;
  color: #ffffff;
  border: none;
  border-radius: 50rpx;
}

.mode-selector button.active {
  background-color: #ff3366;
  color: white;
}

.board-container {
  width: 90vw;
  max-width: 600rpx;
  margin-bottom: 40rpx;
}

.game-board {
  display: grid;
  grid-template-columns: repeat(8, 1fr);
  grid-template-rows: repeat(8, 1fr);
  gap: 5rpx;
  background-color: #2c1a47;
  padding: 10rpx;
  border-radius: 10rpx;
  box-shadow: 0 0 10rpx rgba(75, 0, 130, 0.3);
}

.cell {
  aspect-ratio: 1;
  display: flex;
  align-items: center;
  justify-content: center;
  border-radius: 5rpx;
  font-size: 20rpx;
  position: relative;
  color: #ffffff;
}

.cell.normal {
  background: linear-gradient(135deg, #333333 0%, #222222 100%);
  border: 1rpx solid #444444;
}

.cell.start {
  background: linear-gradient(135deg, #ff3366 0%, #cc0033 100%);
  color: white;
  border: 2rpx solid #ff6699;
  box-shadow: 0 0 10rpx rgba(255, 51, 102, 0.3);
}

.cell.end {
  background: linear-gradient(135deg, #cc0033 0%, #990000 100%);
  color: white;
  border: 2rpx solid #ff3366;
  box-shadow: 0 0 10rpx rgba(204, 0, 51, 0.3);
}

.cell.special {
  background: linear-gradient(135deg, #660033 0%, #33001a 100%);
  border: 2rpx solid #99004d;
}

.cell-icon {
  width: 60%;
  height: 60%;
}

.chess-container {
  position: absolute;
  width: 60rpx;
  height: 60rpx;
  display: flex;
  align-items: center;
  justify-content: center;
}

.chess-icon {
  width: 100%;
  height: 100%;
  border-radius: 50%;
}

.dice-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  margin-bottom: 40rpx;
  perspective: 1000rpx;
}

.dice-text {
  font-size: 32rpx;
  color: #ffffff;
}

.control-container {
  width: 80%;
  display: flex;
  justify-content: space-around;
}

.btn {
  background-color: #ff3366;
  color: white;
  border: none;
  border-radius: 50rpx;
  font-size: 32rpx;
  padding: 20rpx 0;
  width: 45%;
}

.btn.back {
  background-color: #333333;
  color: #ff3366;
}

/* 启动弹窗样式 */
.start-modal-mask {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.5);
  z-index: 9998;
}

.start-modal-container {
  position: fixed;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  width: 85%;
  max-width: 600rpx;
  background-color: white;
  border-radius: 20rpx;
  padding: 30rpx;
  z-index: 9999;
}

.start-modal-content {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.start-modal-title {
  font-size: 36rpx;
  font-weight: bold;
  color: #ff3366;
  margin-bottom: 20rpx;
}

.start-modal-text {
  font-size: 28rpx;
  color: #333333;
  margin-bottom: 30rpx;
  line-height: 1.5;
}

.start-modal-text p {
  margin-bottom: 15rpx;
}

.link {
  color: #ff3366;
  text-decoration: underline;
}

.start-modal-btn {
  background-color: #ff3366;
  color: white;
  border: none;
  border-radius: 50rpx;
  font-size: 32rpx;
  padding: 20rpx 0;
  width: 60%;
}

/* 弹窗样式 */
.modal-mask {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.5);
  z-index: 9998;
}

.modal-container {
  position: fixed;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  width: 80%;
  max-width: 500rpx;
  background-color: white;
  border-radius: 20rpx;
  padding: 30rpx;
  z-index: 9999;
}

.modal-content {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.modal-title {
  font-size: 36rpx;
  font-weight: bold;
  color: #ff3366;
  margin-bottom: 20rpx;
}

.modal-text {
  font-size: 30rpx;
  color: #333333;
  text-align: center;
  margin-bottom: 30rpx;
  line-height: 1.5;
}

.modal-btn {
  background-color: #ff3366;
  color: white;
  border: none;
  border-radius: 50rpx;
  font-size: 32rpx;
  padding: 20rpx 0;
  width: 60%;
}
</style>