<template>
  <div>
    <!-- style="position: absolute; top: 0; left: 0"  -->
    <canvas ref="liveCanvas" />
    <button @click="leftcombilewall">左爬墙</button>
    <button @click="leftcombilewallsaying">左爬墙说</button>
    <button @click="leftcombilewallboring">左爬墙无聊</button>
    <button @click="leftcombilewallhearing">左爬墙听</button>
    <button @click="leftcombilewallreset">左爬重置</button>
    <button @click="rightcombilewall">右爬墙</button>
    <button @click="rightcombilewallsaying">右爬墙说</button>
    <button @click="rightcombilewallboring">右爬墙无聊</button>
    <button @click="rightcombilewallhearing">右爬墙听</button>
    <button @click="rightcombilewallreset">右爬重置</button>
    <button @click="motionreset">动画重置</button>
    <button @click="changModel">切换模型</button>
    <button @click="saying">说话</button>
    <button @click="hearing">听</button>
    <button @click="jumpout">跳出</button>
    <button @click="jumpback">跳回去</button>
  </div>
</template>
<script>
import * as PIXI from 'pixi.js'
import { Live2DModel } from 'pixi-live2d-display/cubism4'
import { MotionPriority } from 'pixi-live2d-display'

window.PIXI = PIXI // 为了pixi-live2d-display内部调用
let app // 为了存储pixi实例
export let model // 为了存储live2d实例
let characterId = 1
let currentMotion = ''
export default {
  async mounted() {
    // 获取URL参数
    const queryParams = new URLSearchParams(window.location.search)
    // 获取名为 "characterid" 的查询参数的值
    characterId = queryParams.get('characterid')
    console.log('character id')
    console.log(characterId)
    app = new PIXI.Application({
      view: this.$refs.liveCanvas,
      transparent: true,
      autoStart: true,
      antialias: true,
      backgroundAlpha: 0
      //  with:1300,
      //  height:1000,
    })

    //app.renderer.backgroundColor = 0x061698;
    // 打包后live2d资源会出现在dist/下，这里用相对路径就能引用到了
    let characterName = 'NIKA'
    if (characterId == 1) {
      characterName = 'NEKO'
    } else if (characterId == 2) {
      characterName = 'NIKA'
    }
    model = Live2DModel.fromSync(`./${characterName}/${characterName}.model3.json`)

    model.once('ready', () => {
      console.log('模型准备完成')
      //model.x = -55
      // model.y = -80
      // app.renderer.view.style.position = 'absolute'
      // app.renderer.view.style.display = 'block'
      // app.renderer.autoResize = true
      // app.renderer.resize(window.innerWidth, window.innerHeight)
      app.stage.addChild(model)
      model.scale.set(0.045) // 调整缩放比例，一般原始资源尺寸非常大，需要缩小
      model.motion('JUMP_OUT')

      model.internalModel.motionManager.on('motionStart', (group, index, audio) => {
        currentMotion = group
        console.log(`motion start ---> group:${group} index:${index} audio:${audio}`)
      })
      model.internalModel.motionManager.on('motionFinish', (v) => {
        if (currentMotion == 'JUMP_BACK') {
          app.stage.removeChild(model)
          //model.destroy()
          // model.stopAllMotions()
          console.log('通知客户端 模型跳出动画播放结束')
        }
        currentMotion = ''
        console.log(`motion finish ---> v: ${v}`)
      })
      model.on('hit', (hitAreas) => {
        console.log('触发点击区域')
        if (hitAreas.includes('Head')) {
          console.log('------->')
          // model.motion('touch_head')
        }
        if (hitAreas.includes('Body')) {
          console.log('----> touch body --->')
        }
      })
    })
    //model = await Live2DModel.from('./sealion/hgkoazarasi.model3.json')

    //model = await Live2DModel.from('https://download.lynksoul.com/models/Nika.zip')

    //app.renderer.view.with=500;
    //app.renderer.view.height=1000;

    // app.stage.addChild(model)
  },
  methods: {
    async changModel() {
      app.stage.removeChildren()
      let characterName = 'NIKA'
      if (characterId == 1) {
        characterId = 2
      } else {
        characterId = 1
      }
      if (characterId == 1) {
        characterName = 'NEKO'
      } else if (characterId == 2) {
        characterName = 'NIKA'
      }
      let newModel = await Live2DModel.from(`./${characterName}/${characterName}.model3.json`)
      newModel.x = -55
      newModel.y = -80
      app.stage.removeChild(model)
      app.stage.addChild(newModel)
      newModel.scale.set(0.045) // 调整缩放比例，一般原始资源尺寸非常大，需要缩小
      model = newModel
      newModel = null
      console.log('切换模型')
    },
    leftcombilewall() {
      model.motion('LEFT')
    },
    leftcombilewallhearing() {
      model.motion('LEFT_HEARING')
    },
    leftcombilewallsaying() {
      model.motion('LEFT_SAYING')
    },
    leftcombilewallboring() {
      model.motion('LEFT_BORING')
    },
    leftcombilewallreset() {
      model.motion('LEFT_RESET')
    },
    rightcombilewall() {
      model.motion('RIGHT')
    },
    rightcombilewallhearing() {
      model.motion('RIGHT_HEARING')
    },
    rightcombilewallsaying() {
      model.motion('RIGHT_SAYING')
    },
    rightcombilewallboring() {
      model.motion('RIGHT_BORING')
    },
    rightcombilewallreset() {
      model.motion('RIGHT_RESET')
    },
    motionreset() {
      model.motion('RESET')
    },
    saying() {
      model.motion('CENTER_SAYING')
    },
    hearing() {
      model.motion('CENTER_HEARING')
    },
    jumpback() {
      model.motion('JUMP_BACK')
    },
    jumpout() {
      model.motion('JUMP_OUT')
      // model._startMotion()
    }
  }
}
// //禁止右击
// document.addEventListener('contextmenu', (event) => {
//   event.preventDefault()
//   console.log('右击---->')
//   window.chrome.webview.hostObjects.csobj.RightClick()
// })
// let currentMotion = ''
// window.chrome.webview.addEventListener('message', (arg) => {
//   var exp = arg.data['expression']
//   if (!exp) {
//     exp = 'NORMAL'
//   }
//   console.log('执行表情 ： ' + exp)
//   model.expression('Reset')
//   model.expression(exp)
//   var motionExt = arg.data['motion']
//   //if(motionExt=='CENTER_Breathing') return;

//   if (motionExt) {
//     if (currentMotion.includes('RIGHT') || currentMotion.includes('LEFT')) {
//       if (motionExt.includes('RIGHT') || motionExt.includes('LEFT')) {
//         model.motion('HeadReset', undefined, MotionPriority.FORCE)
//         console.log('head expression reset')
//       } else {
//         model.motion('Reset', undefined, MotionPriority.FORCE)
//         console.log('DragUp  Reset')
//       }
//     } else {
//       console.log('Reset')
//       model.motion('Reset', undefined, MotionPriority.FORCE)
//     }
//     console.log('执行动画 ： ' + motionExt)
//     model.motion(motionExt, undefined, MotionPriority.FORCE)
//     currentMotion = motionExt
//   }
// })
</script>
<style>
::-webkit-scrollbar {
  display: none;
}
</style>
