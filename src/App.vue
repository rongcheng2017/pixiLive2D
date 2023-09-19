<template>
  <div >
    <!-- style="position: absolute; top: 0; left: 0;" -->
    <canvas ref="liveCanvas" style="position: absolute; top: 0; left: 0;" />
    <!-- <button @click="expressHappy">开心</button>
    <button @click="leftcombilewall">左爬墙</button>
    <button @click="rightcombilewall">右爬墙</button>
    <button @click="drag">拽起</button>
    <button @click="jumpOut">跳出</button>
    <button @click="jumpBack">调回</button>
    <button @click="waveDance">挥手舞</button>
    <button @click="hearing">聆听</button>
    <button @click="addExpression">添加表情</button>
    <button @click="remove">取消表情</button>
    <button @click="idle">空闲</button>
    <button @click="talk">说话</button> -->
  </div>
</template>
<script>
import * as PIXI from 'pixi.js'
import { Live2DModel } from 'pixi-live2d-display/cubism4'

window.PIXI = PIXI // 为了pixi-live2d-display内部调用

let app // 为了存储pixi实例
let model // 为了存储live2d实例

export default {
  async mounted() {
    app = new PIXI.Application({
      view: this.$refs.liveCanvas,
      transparent: true,
      autoStart: true,
      antialias:true,
      backgroundAlpha: 0,
      //  with:1300,
      //  height:1000,
    })
    //app.renderer.backgroundColor = 0x061698;
    // 打包后live2d资源会出现在dist/下，这里用相对路径就能引用到了
    model = await Live2DModel.from('./Nika/NIKA.model3.json')
    //model = await Live2DModel.from('./Mao/Mao.model3.json')
    model.x =-55;
    model.y =-80;
    //app.renderer.view.with=500;
    //app.renderer.view.height=1000;
    app.renderer.view.style.position = "absolute";
    app.renderer.view.style.display = "block";
    app.renderer.autoResize = true;
     app.renderer.resize(window.innerWidth, window.innerHeight);
    app.stage.addChild(model)
    model.scale.set(0.045) // 调整缩放比例，一般原始资源尺寸非常大，需要缩小
    model.on('hit', hitAreas => {
      if (hitAreas.includes('Head')) {
        console.log("------->")
       // model.motion('touch_head')
      }
      if(hitAreas.includes('Body')){
        console.log("----> touch body --->")
      }
    });
  },
  methods: {
    expressHappy() {
      // 调用Live2D模型的表情方法，这里你可以根据需要传递不同的表情类型
      model.motion('TapHead',0)
      console.log("TapHead");
    },
    leftcombilewall() {
      // 调用Live2D模型的表情方法，这里你可以根据需要传递不同的表情类型
      model.motion('LeftClimbWall')
      console.log("leftcombilewall");
    },
    rightcombilewall() {
      // 调用Live2D模型的表情方法，这里你可以根据需要传递不同的表情类型
      model.motion('RightClimbWall')
      console.log("rightcombilewall");
    },
    drag() {
      // 调用Live2D模型的表情方法，这里你可以根据需要传递不同的表情类型
     // model.motion('Drag',undefined, MotionPriority.FORCE);
      model.motion('Drag',0);
      console.log("drag");
    },
    jumpBack() {
      model.motion("JumpBack");
    },
    jumpOut() {
      model.motion("JumpOut");
    },
    waveDance(){
      model.motion("WavingDance");
     // model.expression("sleep");
    },
    hearing(){
      console.log("Hearing---->");
      model.motion("Hearing");
    },
    addExpression(){
      console.log("add expression");
      model.expression("sleep");
    },
    remove(){
      model.expression();
    },
    idle(){
      console.log("idle-->");
      model.motion("Idle");
    },
    talk(){
      console.log("talking-->");
      model.motion("Saying")
    }
  }
}
//静止右击
document.addEventListener("contextmenu", (event) => {
         event.preventDefault();
         console.log("右击---->");
         window.chrome.webview.hostObjects.csobj.RightClick();
      });

window.chrome.webview.addEventListener('message', arg => {
    //document.querySelector(".outer").innerHTML = arg.data.color;
    //model.motion("JumpBack");
    console.log(arg.data);
    //debugger
    var motionExt=arg.data['motion'];
    if (motionExt) {
      console.log("执行动画 ： "+motionExt);

        model.motion(motionExt);
    }
    var exp = arg.data['expression'];
    if(!exp){
      exp= "NORMAL"
    }
    console.log("执行表情 ： "+exp);
    model.expression(exp);
  }
    
  );      


</script>
<style>
::-webkit-scrollbar {
  display: none;
}
</style>