<template>
  <div>
    <!-- style="position: absolute; top: 0; left: 0"  -->
    <canvas ref="liveCanvas" style="position: absolute; top: 0; left: 0" />
    <!-- <button @click="leftcombilewall">左爬墙</button>
    <button @click="leftcombilewallsaying">左爬墙说</button>
    <button @click="leftcombilewallboring">左爬墙无聊</button>
    <button @click="leftcombilewallhearing">左爬墙听</button>
    <button @click="rightcombilewall">右爬墙</button>
    <button @click="rightcombilewallsaying">右爬墙说</button>
    <button @click="rightcombilewallboring">右爬墙无聊</button>
    <button @click="rightcombilewallhearing">右爬墙听</button>
    <button @click="headreset">头部动画重置</button>
    <button @click="motionreset">动画重置</button>
    <button @click="changModel">切换模型</button>
    <button @click="holdphone">拿手机</button>
    <button @click="dropphone">放手机</button>
    <button @click="saying">说话</button>
    <button @click="hearing">听</button>
    <button @click="jumpout">跳出</button>
    <button @click="jumpback">跳回去</button>
    <button @click="angry">生气</button>
    <button @click="happy">开心</button>
    <button @click="expreset">表情重置</button> -->
  </div>
</template>
<script>
import * as PIXI from 'pixi.js'
import { Live2DModel } from 'pixi-live2d-display/cubism4'
import { MotionPriority } from 'pixi-live2d-display'

window.PIXI = PIXI // 为了pixi-live2d-display内部调用
let app // 为了存储pixi实例
export let model // 为了存储live2d实例
let modelJsonUrl = ""
let postionX = 0;
let postionY = 0;
let modelScale = 1;
let catalogType = -1;
let currentMotion = ''
export default {
  async mounted() {
    // 获取URL参数
    const queryParams = new URLSearchParams(window.location.search)
    // 获取名为 "characterid" 的查询参数的值
    modelJsonUrl = queryParams.get('model_json')??"./public/Neko/Neko.model3.json"
    //modelJsonUrl = "./Nika/Nika.model3.json"
    postionX = queryParams.get('x') ?? -55
    postionY = queryParams.get('y') ?? -80
    modelScale = queryParams.get('scale') ?? 0.045
    catalogType = queryParams.get('catalog') ?? -1;
    console.log(`modelJsonUrl ${modelJsonUrl} x:${postionX} y:${postionY} scale:${modelScale}`)
    app = new PIXI.Application({
      view: this.$refs.liveCanvas,
      transparent: true,
      autoStart: true,
      antialias: true,
      backgroundAlpha: 0
    })

    //app.renderer.backgroundColor = 0x061698;
    loadMode(modelJsonUrl);
  },
  methods: {
    changModel() {
      if (characterId == 1) {
        characterId = 2
      } else {
        characterId = 1;
      }
      app.stage.removeChild(model)
      loadMode(characterId)
    },
    leftcombilewall() {
      model.motion('LEFT', undefined, MotionPriority.FORCE)
    },
    leftcombilewallhearing() {
      model.motion('LEFT_HEARING', undefined, MotionPriority.FORCE)
    },
    leftcombilewallsaying() {
      model.motion('LEFT_SAYING', undefined, MotionPriority.FORCE)
    },
    leftcombilewallboring() {
      model.motion('LEFT_BORING', undefined, MotionPriority.FORCE)
    },
    leftcombilewallreset() {
      model.motion('LEFT_RESET', undefined, MotionPriority.FORCE)
    },
    rightcombilewall() {
      model.motion('RIGHT', undefined, MotionPriority.FORCE)
    },
    rightcombilewallhearing() {
      model.motion('RIGHT_HEARING', undefined, MotionPriority.FORCE)
    },
    rightcombilewallsaying() {
      model.motion('RIGHT_SAYING', undefined, MotionPriority.FORCE)
    },
    rightcombilewallboring() {
      model.motion('RIGHT_BORING', undefined, MotionPriority.FORCE);
    },
    rightcombilewallreset() {
      model.motion('RIGHT_RESET', undefined, MotionPriority.FORCE);
    },
    motionreset() {
      model.motion('RESET', undefined, MotionPriority.FORCE)
    },
    saying() {
      model.motion('CENTER_SAYING', undefined, MotionPriority.FORCE)
    },
    hearing() {
      model.motion('CENTER_HEARING', undefined, MotionPriority.FORCE)
    },
    jumpback() {
      model.motion('JUMP_BACK', undefined, MotionPriority.FORCE)
    },
    jumpout() {
      model.motion('JUMP_OUT', undefined, MotionPriority.FORCE)
    },
    headreset() {
      model.motion('HEAD_RESET', undefined, MotionPriority.FORCE)
    },
    happy() {
      model.expression('HAPPY')
    },
    angry() {
      model.expression('ANGRY')
    },
    expreset() {
      model.expression('EXP_RESET')
    },
    holdphone() {
      model.motion('CENTER_HoldPhone', undefined, MotionPriority.FORCE)
    },
    dropphone() {
      model.motion('CENTER_DropPhone', undefined, MotionPriority.FORCE)
    }
  }
}
 var storeMotion=null;
function loadMode(modelUrl) {
  model = Live2DModel.fromSync(modelUrl)
  model.once('ready', () => {
    console.log('模型准备完成')
    model.x = postionX
    model.y = postionY
    app.renderer.view.style.position = 'absolute'
    app.renderer.view.style.display = 'block'
    app.renderer.autoResize = true
    app.renderer.resize(window.innerWidth, window.innerHeight)
    app.stage.addChild(model)
    model.scale.set(modelScale) // 调整缩放比例，一般原始资源尺寸非常大，需要缩小
    model.motion('JUMP_OUT');
    console.log('切换模型完成')
    changeMode = false;
    model.internalModel.motionManager.on('motionStart', (group, index, audio) => {
      currentMotion = group;
      console.log(`StoreMotion is ${storeMotion}`);
      //左右爬墙前需要将头部动画都归位
      if((group=='LEFT'||group=='RIGHT'||group=='CENTER_SAYING')&&!storeMotion){
        storeMotion = group;
        currentMotion ='HEAD_RESET'
        model.motion(currentMotion, undefined, MotionPriority.FORCE);
        return;
      }
      if(currentMotion==storeMotion){
        storeMotion = null;
      }
      console.log(`motion start ---> motion:${currentMotion} index:${index} audio:${audio}`)
    }
    );
    
    model.internalModel.motionManager.on('motionFinish', (v) => {
      if (currentMotion == 'JUMP_BACK') {
        app.stage.removeChild(model)
        console.log('模型跳出动画播放结束')
        if (changeMode == true && modelJsonUrl) {
          loadMode(modelJsonUrl);
        }
      }
      //执行延迟执行的动画
      if((currentMotion=='HEAD_RESET')&&storeMotion){
        var motion = storeMotion;
        model.motion(motion, undefined, MotionPriority.FORCE);   
        return;
      }
      //虚拟人从墙上拖拽下来后，需要将之前的动画归位     
      if(currentMotion=='CENTER'){
        model.motion('RESET', undefined, MotionPriority.FORCE);   
        return;
      } 
      currentMotion = ''
      console.log(`motion finish ---> v: ${v}`)
    });
    
    model.on('pointerdown',(a)=>{
      if(a.data.buttons ==2){
        console.log("右击");
      }else{
        const { x, y } = a.data.global 
        const point = model.hitTest(x, y)
        if (point.includes('Head')) {
        model.motion('TapHead', undefined, MotionPriority.FORCE)
        console.log('-------> touch head -?--->')
        }
        if (point.includes('Body')) {
          model.motion('TapBody', undefined, MotionPriority.FORCE)
          console.log('----> touch body --->')
        }
        console.log("左击");
      }
      
      
    });
  });
}
let changeMode = false;
function changModel(modelInfo) {
  var newModelJsonUrl = modelInfo['model_json'];
  if (modelJsonUrl == newModelJsonUrl || !modelJsonUrl) return;
  postionX = modelInfo['x'] ?? 0;
  postionY = modelInfo['y'] ?? 0;
  modelScale = modelInfo['scale'] ?? 1;
  //name = modelInfo['name'] ?? "";
  
  if (catalogType == 1) {//官方模型才有跳出动画
    changeMode = true;
    modelJsonUrl = newModelJsonUrl;
    model.motion('JUMP_BACK',undefined, MotionPriority.FORCE);
  } else {
    app.stage.removeChild(model);
    loadMode(newModelJsonUrl);
  }
  catalogType = modelInfo['catalog_id'] ?? -1;

}
//禁止右击
// document.addEventListener('contextmenu', (event) => {
//   event.preventDefault()
//   console.log('右击------->')
//   //window.chrome.webview.hostObjects.csobj.RightClick()
// })
window.chrome.webview.addEventListener('message', (arg) => {
  console.log(arg.data);
  var motionExt = arg.data['motion']
  //切换模型
  if (motionExt == 'CHANGE_MODEL') {
    console.log("切换模型");
    var modelInfo = arg.data['model_info'];
    if (!modelInfo) return;
    changModel(modelInfo);
    return;
  }
  //执行表情
  var exp = arg.data['expression']
  if (!exp) {
    exp = 'NORMAL'
  }
  console.log('执行表情 ： ' + exp)
  model.expression('EXP_RESET')
  model.expression(exp)
  //执行动作
  if (motionExt) {
    // if (currentMotion.includes('RIGHT') || currentMotion.includes('LEFT')||currentMotion.includes('DragUp')) {
    //   if (motionExt.includes('RIGHT') || motionExt.includes('LEFT')) {
    //     model.motion('HEAD_RESET', undefined, MotionPriority.FORCE)
    //     console.log('HEAD_RESET')
    //   } else {
    //     model.motion('RESET', undefined, MotionPriority.FORCE)
    //     console.log('Drag Down Reset')
    //   }
    // } else {
    //   console.log('执行动画: Reset')
    //   model.motion('RESET', undefined, MotionPriority.FORCE)
    // }
    console.log('执行动画 ： ' + motionExt)
    model.motion(motionExt, undefined, MotionPriority.FORCE)
    currentMotion = motionExt

  }
})
document.body.addEventListener('mousedown', evt => {
    if(evt.button==0)
    {
      window.chrome.webview.postMessage('leftBtnDown');
      evt.preventDefault();
      evt.stopPropagation();

    }
    else if(evt.button==2)
        window.chrome.webview.postMessage('rightBtnDown');                                   
    });                           
document.body.addEventListener('mouseup', evt => {
    window.chrome.webview.postMessage('leftBtnUp');
});


</script> 
<style>
::-webkit-scrollbar {
  display: none;
}
</style> 
