<template>
  <view class="game-container">
    <!-- 启动弹窗 -->
    <view class="start-modal-mask" v-if="showStartModal"></view>
    <view class="start-modal-container" v-if="showStartModal">
      <view class="start-modal-content">
        <view class="start-modal-title">高级版（仅限于18岁以上使用）</view>
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
import Dice3D from '@/components/Dice3D.vue';
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
      currentMode: 'gaoji', // 当前模式
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
      
      // 设置高级版路线 (回字形路线)
      this.setAdvancedRoute(cells);
      
      this.boardCells = cells;
      this.boyPosition = 0;
      this.girlPosition = 0;
      this.updateChessPositions();
    },
    
    // 设置高级版路线 (回字形)
    setAdvancedRoute(cells) {
      // 高级版采用回字形路线
      // 这里可以设置格子的视觉排列顺序
    },
    
    // 获取格子类型
    getCellType(index) {
      // 普通格子
      let type = 'normal';
      
      // 起点和终点
      if (index === 0) return 'start';
      if (index === this.boardSize - 1) return 'end';
      
      // 每隔5个格子设置为特殊格子
      if (index % 5 === 0) {
        type = 'special';
      }
      
      return type;
    },
    
    // 设置特殊格子
    setSpecialCells(cells) {
      // 在一些位置设置特殊格子 (不使用图标)
      // 调整位置以适应0-60的中间格子
      const specialPositions = [4, 9, 14, 19, 24, 29, 34, 39, 44, 49, 54, 59];
      specialPositions.forEach(pos => {
        if (pos < cells.length) {
          // 这里可以设置不同类型的特殊格子
          cells[pos].icon = null; // 不使用图标
        }
      });
    },
    
    // 处理骰子滚动结束
    handleRollEnd(value) {
      this.diceValue = value;
      this.moveChess();
    },
    
    // 重置骰子
    resetDice() {
      if (this.$refs.dice) {
        this.$refs.dice.reset();
      }
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
      uni.navigateTo({ url: '/pages/game/gaoji_rules' });
    },
    
    // 根据位置显示弹窗内容
    showModalContent(position) {
      // 定义各个位置对应的内容
      const positionContents = {
        1: '舔对方脖子到胸30秒',
        2: '对镜子拍揉胸视频30秒',
        3: '翘起pp让任意让对方打5下',
        4: '自拍亲吻视频10秒',
        5: '【福利】对方给你口指定部位3分钟',
        6: '涂上油胸对胸给对方胸推一分钟',
        7: '吸吮对方手指20秒',
        8: '自己用手扣下面1分钟或撸1分钟',
        9: '口含热水给对方口30秒',
        10: '回到起点',
        11: '自己使用按摩棒（跳蛋）按摩私处1分钟',
        12: '和对方湿吻30秒并拍视频',
        13: '对对方用牙齿咬住乳头磨蹭10秒',
        14: '女穿情趣内衣男舔她后背1分钟',
        15: '前进到20',
        16: '自拍一段接吻小视频20秒',
        17: '后入抽插30秒不许射',
        18: '对方站着自己跪着帮对方口1分钟',
        19: '从背后伸手过来揉胸1分钟',
        20: '【福利】对方含着水在你身上亲吻1分钟',
        21: '对方使用按摩棒（跳蛋）按摩自己敏感部位1分钟',
        22: '被对方用jj抽脸5下或bb蹭脸10秒',
        23: '将酸奶倒在胸上对方舔干净',
        24: '吸吮对方脚趾并拍视频10秒',
        25: '回到22',
        26: '后入抽插1分钟不许射',
        27: '揉对方胸3下或轻咬耳垂5下',
        28: '观音坐莲30秒不许射',
        29: '用嘴含住对方蛋蛋或阴蒂10秒',
        30: '直达终点',
        31: '指定对方任意体位抽插30秒',
        32: '用胸或jj蹭对方脸30秒',
        33: '用尽办法让对方勃起或流水',
        34: '拍一段给对方口交的视频30秒',
        35: '【惩罚】舔遍对方全身每一寸肌肤',
        36: '老汉推车30秒不许射',
        37: '舔对方大腿内侧20秒',
        38: '对方帮你口指定部位并拍视频',
        39: '对方给你撸管或扣私处30秒',
        40: '回到起点',
        41: '拍一段和对方爱爱的视频30秒',
        42: '女用大腿磨蹭男方私处30秒',
        43: '露脸拍一段给对方口的视频',
        44: '亲吻对方丝足或jj30秒',
        45: '【福利】躺下享受被对方舔遍全身',
        46: '摆一个诱惑的姿势被对方拍照',
        47: '同时舔对方胸并抚摸下体',
        48: '用力吸男方龟头或对女方私处吹气',
        49: '挑逗对方敏感部位直到对方求饶',
        50: '直达50',
        51: '翘起pp让任意让对方抚摸30秒',
        52: '录制对方自w视频1分钟',
        53: '分开双腿让对方仔细观察下体30秒',
        54: '女生给男生乳交10秒并拍视频',
        55: '亲吻对方的屁股',
        56: '给对方脖子处吸一个草莓',
        57: '把酒倒在自己身上对方舔干净',
        58: '钻进对方怀里撒娇',
        59: '亲吻对方的屁股',
        60: '回到起点',
        61: '获胜方请指定自己喜欢的体位尽情享受做爱的快乐~'
      };
      
      // 特殊跳转位置
      const specialJumps = {
        10: 0,   // 回到起点
        15: 20,  // 前进到20
        25: 22,  // 回到22
        30: 61,  // 直达终点
        40: 0,   // 回到起点
        50: 50,  // 直达50
        60: 0    // 回到起点
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
  background-color: #e6f7ff;
  min-height: 100vh;
}

.game-title {
  font-size: 48rpx;
  font-weight: bold;
  margin: 30rpx 0;
  color: #1890ff;
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
  background-color: #bae7ff;
  border: none;
  border-radius: 50rpx;
}

.mode-selector button.active {
  background-color: #1890ff;
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
  background-color: #e6f7ff;
  padding: 10rpx;
  border-radius: 10rpx;
  box-shadow: 0 0 10rpx rgba(0, 0, 0, 0.1);
}

.cell {
  aspect-ratio: 1;
  display: flex;
  align-items: center;
  justify-content: center;
  border-radius: 5rpx;
  font-size: 20rpx;
  position: relative;
}

.cell.normal {
  background-color: #e6f7ff;
  border: 1rpx solid #bae7ff;
}

.cell.start {
  background: linear-gradient(135deg, #1890ff 0%, #0050b3 100%);
  color: white;
  border: 2rpx solid #40a9ff;
  box-shadow: 0 0 10rpx rgba(24, 144, 255, 0.3);
}

.cell.end {
  background: linear-gradient(135deg, #0050b3 0%, #003a8c 100%);
  color: white;
  border: 2rpx solid #1890ff;
  box-shadow: 0 0 10rpx rgba(0, 80, 179, 0.3);
}

.cell.special {
  background: linear-gradient(135deg, #91d5ff 0%, #40a9ff 100%);
  border: 2rpx solid #1890ff;
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
  color: #333333;
}

.control-container {
  width: 80%;
  display: flex;
  justify-content: space-around;
}

.btn {
  background-color: #1890ff;
  color: white;
  border: none;
  border-radius: 50rpx;
  font-size: 32rpx;
  padding: 20rpx 0;
  width: 45%;
}

.btn.back {
  background-color: #bae7ff;
  color: #1890ff;
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
  color: #1890ff;
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
  background-color: #1890ff;
  color: white;
  border: none;
  border-radius: 50rpx;
  font-size: 32rpx;
  padding: 20rpx 0;
  width: 60%;
}

/* 启动弹窗样式 */
.start-modal-mask {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.7);
  z-index: 9999;
}

.start-modal-container {
  position: fixed;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  width: 90%;
  max-width: 600rpx;
  background-color: white;
  border-radius: 20rpx;
  padding: 30rpx;
  z-index: 10000;
}

.start-modal-content {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.start-modal-title {
  font-size: 36rpx;
  font-weight: bold;
  color: #1890ff;
  margin-bottom: 20rpx;
  text-align: center;
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
  color: #1890ff;
  text-decoration: underline;
}

.start-modal-btn {
  background-color: #1890ff;
  color: white;
  border: none;
  border-radius: 50rpx;
  font-size: 32rpx;
  padding: 20rpx 0;
  width: 60%;
}
</style>