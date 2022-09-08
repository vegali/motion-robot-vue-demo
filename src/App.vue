<template>
  <div id="app">
    <div ref="robot"></div>
    <div>
      <span>选择Action：</span>
      <select v-model="actionIndex">
        <option :value="0" selected>动作A</option>
        <option :value="1">动作B</option>
        <option :value="2">动作C</option>
        <option :value="3">动作D</option>
        <option :value="4">动作F</option>
        <option :value="5">动作H</option>
        <option :value="6">动作G</option>
        <option :value="7">动作E</option>
      </select>
    </div>
    <div>
      <button v-on:click="doStartAnim">开始Action</button>
      <button v-on:click="doStopAnim">结束Action</button>
      <button v-on:click="doChangeAudioVolume">切换音量</button>
    </div>
    <div>
      <input type="text" v-model="question">
      <button v-on:click="doBroadCast">播报问题</button>
      <button v-on:click="doGetAnswerAccordText()">获取答案</button>
    </div>
    <div>
      <button v-on:click="doRecOpen()">打开录音权限</button>
      <button v-on:click="doRecStart()">录制</button>
      <button v-on:click="doRecStop()">停止</button>
      <button v-on:click="doRecClose()">释放录音资源</button>
    </div>
  </div>
</template>

<script>
import * as Robot from 'motion-robot'
import Recorder from 'recorder-core/recorder.wav.min'

export default {
  name: 'App',
  data() {
    return {
      rec: null,
      volume: 1,
      question: '',
      actionIndex: 0,
      actionArr: [
        {
          actionName: 'A',
          audioUrl: 'http://ds-model-tts.oss-cn-beijing.aliyuncs.com/poc/mengguang/af66e1fb66ff4bbe93cb05be6e5da0f5.wav',
          mouthUrl: 'http://ds-vhost-action-dev.oss-cn-beijing.aliyuncs.com/af66e1fb66ff4bbe93cb05be6e5da0f5.json'
        },
        {
          actionName: 'B',
          audioUrl: 'http://ds-model-tts.oss-cn-beijing.aliyuncs.com/poc/mengguang/af66e1fb66ff4bbe93cb05be6e5da0f5.wav',
          mouthUrl: 'http://ds-vhost-action-dev.oss-cn-beijing.aliyuncs.com/af66e1fb66ff4bbe93cb05be6e5da0f5.json'
        },
        {
          actionName: 'C',
          audioUrl: 'http://ds-model-tts.oss-cn-beijing.aliyuncs.com/poc/mengguang/af66e1fb66ff4bbe93cb05be6e5da0f5.wav',
          mouthUrl: 'http://ds-vhost-action-dev.oss-cn-beijing.aliyuncs.com/af66e1fb66ff4bbe93cb05be6e5da0f5.json'
        },
        {
          actionName: 'D',
          audioUrl: 'http://ds-model-tts.oss-cn-beijing.aliyuncs.com/poc/mengguang/af66e1fb66ff4bbe93cb05be6e5da0f5.wav',
          mouthUrl: 'http://ds-vhost-action-dev.oss-cn-beijing.aliyuncs.com/af66e1fb66ff4bbe93cb05be6e5da0f5.json'
        },
        {
          actionName: 'F',
          audioUrl: 'https://ds-model-tts.oss-cn-beijing.aliyuncs.com/poc/18203610571/c1c6529fd5f7403094ce7dc2e43b3bf2.wav',
          mouthUrl: 'https://ds-vhost-action-dev.oss-cn-beijing.aliyuncs.com/c1c6529fd5f7403094ce7dc2e43b3bf2.json'
        },
        {
          actionName: 'H',
          audioUrl: 'http://ds-model-tts.oss-cn-beijing.aliyuncs.com/poc/mengguang/af66e1fb66ff4bbe93cb05be6e5da0f5.wav',
          mouthUrl: 'http://ds-vhost-action-dev.oss-cn-beijing.aliyuncs.com/af66e1fb66ff4bbe93cb05be6e5da0f5.json'
        },
        {
          actionName: 'G',
          audioUrl: 'http://ds-model-tts.oss-cn-beijing.aliyuncs.com/poc/mengguang/af66e1fb66ff4bbe93cb05be6e5da0f5.wav',
          mouthUrl: 'http://ds-vhost-action-dev.oss-cn-beijing.aliyuncs.com/af66e1fb66ff4bbe93cb05be6e5da0f5.json'
        },
        {
          actionName: 'E',
          audioUrl: 'http://ds-model-tts.oss-cn-beijing.aliyuncs.com/poc/mengguang/af66e1fb66ff4bbe93cb05be6e5da0f5.wav',
          mouthUrl: 'http://ds-vhost-action-dev.oss-cn-beijing.aliyuncs.com/af66e1fb66ff4bbe93cb05be6e5da0f5.json'
        }
      ]
    }
  },
  mounted() {
    // 配置信息
    Robot.configData({
      audioFileFormat: 'wav',
      serverUserId: '15001191540'
    })
    // 请求服务数据
    Robot.getServerConfigData(data => {
      // 初始化模型
      Robot.initModel(this.$refs.robot, 500, 500, e => {
        console.log(e)
      })
    })
  },
  methods: {
    doStartAnim() {
      console.log(this.actionIndex)
      Robot.startAnim(this.actionArr[this.actionIndex].actionName)
    },
    doStopAnim() {
      Robot.stopAnim()
    },
    doChangeAudioVolume() {
      if (this.volume === 1) {
        this.volume = 0
      } else {
        this.volume = 1
      }
      Robot.changeAudioVolume(this.volume)
      console.log(this.volume)
    },
    doGetAnswerAccordText() {
      Robot.getAnswerAccordText(this.question)
    },
    doRecOpen() {
      this.rec = null
      let newRec = Recorder({
        type: 'wav',
        sampleRate: 16000,
        bitRate: 24
      })
      newRec.open(
        () => {
          this.rec = newRec
        },
        msg => {
          alert('录音权限已拒绝：' + msg)
        }
      )
    },
    doRecClose() {
      if (this.rec) {
        this.rec.close()
        this.rec = null
        console.log('资源已释放')
      } else {
        console.log('未打开录音')
      }
    },
    doRecStart() {
      if (this.rec && Recorder.IsOpen()) {
        console.log('开始录音')
        this.rec.start()
      }
    },
    doRecStop() {
      if (this.rec == null || !Recorder.IsOpen()) {
        console.log('未打开录音')
        return
      }
      this.rec.stop((blob, duration) => {
        console.log(`已录制${duration}ms，${blob.size}字节`)
        // console.log(blob);
        let audioRes = {
          blob,
          size: blob.size
        }
        Robot.getAnswerAccordAudio(audioRes, null, this.answerResultCallBack, this.playStartCallback, this.playCompleteCallback)
      })
    },
    answerResultCallBack(e) {
      console.log('answerResultCallBack')
      console.log(e)
    },
    playStartCallback(e) {
      console.log('playStartCallback')
      console.log(e)
    },
    playCompleteCallback(e) {
      console.log('playCompleteCallback')
      console.log(e)
    },
    doBroadCast() {
      Robot.getBoardcastAudio(this.question)
    }
  }
}
</script>
