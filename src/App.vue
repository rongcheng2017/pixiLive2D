<template>
  <div >
    <canvas ref="liveCanvas" style="position: absolute; top: 0; left: 0;" />
  </div>
</template>
<script>
import * as PIXI from 'pixi.js'
import { Live2DModel } from 'pixi-live2d-display/cubism4'
import { MotionPriority } from 'pixi-live2d-display';

window.PIXI = PIXI // 为了pixi-live2d-display内部调用

let app // 为了存储pixi实例
export let model // 为了存储live2d实例

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
    //model = await Live2DModel.from('https://download.lynksoul.com/models/Nika.zip')
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
      console.log("触发点击区域");
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
    
  }
}
//静止右击
document.addEventListener("contextmenu", (event) => {
         event.preventDefault();
         console.log("右击---->");
         window.chrome.webview.hostObjects.csobj.RightClick();
      });
let currentMotion = '';
window.chrome.webview.addEventListener('message', arg => {

  var motionExt=arg.data['motion'];
    if (motionExt) {
      console.log("执行动画 ： "+motionExt);
      if(motionExt=='DragUp'&&(currentMotion.includes('RIGHT')||currentMotion.includes('LEFT'))){
        model.motion('Reset',undefined,MotionPriority.FORCE);
        model.motion(motionExt);
      }else{
        
        model.motion(motionExt,undefined,MotionPriority.FORCE);
      }
      currentMotion= motionExt;
        //model.motion(motionExt);
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