<template>
    <div class="color-picker">
        <div class="card">
            <div class="preview-color" :style="getBackgroundColor()"></div>
            <div class="picker">
                <canvas 
                    ref="picker" 
                    id="picker" 
                    width="150px"
                    v-on:click="(e) => onPickColor(e)"
                    >
                </canvas>
                </div>
            <div class="color-controls">
                <div class="control">
                    R:  <input type="range" v-model.number="color.r" min="0" max="255">
                </div>
                <div class="control" >
                    G:  <input type="range" v-model.number="color.g" min="0" max="255">
                </div>
                <div class="control">
                    B:  <input type="range" v-model.number="color.b"  min="0" max="255">
                </div>
            </div>
        </div>
    </div>
</template>
<script lang="ts">
import Vue from "vue";
import {
  Component,
  Emit,
  Inject,
  Model,
  Prop,
  Provide,
  Watch
} from "vue-property-decorator";
import "./ColorPicker.css";
interface Color {
  r: number;
  g: number;
  b: number;
}
@Component({ name: "color-picker" })
export default class ColorPicker extends Vue {
  canvas: HTMLCanvasElement;
  color: Color;
  @Prop() initialColor: Color;
  @Emit("select")
  onColorSelect() {
      console.log("in ColorPicker");
  }
  mounted() {
      this.canvas = this.$refs.picker as HTMLCanvasElement;
    const context = this.canvas.getContext("2d");
    if (context) {
        renderColorPicker(
            context,
        this.canvas.width / 2,
        this.canvas.height / 2,
        75
      );
    }
  }
  beforeMount() {
      this.color = this.initialColor;
  }

  getBackgroundColor() {
      return {
          background: `rgb(${this.color.r}, ${this.color.g}, ${this.color.b})`
    };
  }
  
  onPickColor(e: MouseEvent) {
    this.color = getColorForMouseClickInCanvas(e, this.canvas);
    this.$emit('select', this.color);
    this.$forceUpdate();
  }
}
function getColorForMouseClickInCanvas(
  e: MouseEvent,
  canvas: HTMLCanvasElement
): Color {
  let ctx = canvas.getContext("2d");
  if (ctx) {
      let canvasX = Math.floor(e.pageX - canvas.offsetLeft);
      let canvasY = Math.floor(e.pageY - canvas.offsetTop);
    
      // get current pixel
      let imageData = ctx.getImageData(canvasX, canvasY, 1, 1);
      let pixel = imageData.data;
    
      return { r: pixel[0], g: pixel[1], b: pixel[2] };
  }
  return {r: 0, g: 0, b: 0};
}
function renderColorPicker(
  context: CanvasRenderingContext2D,
  x: number,
  y: number,
  radius: number = 100
) {
  const counterClockwise = false;
  for (let angle = 0; angle <= 360; angle += 1) {
    let startAngle = (angle - 2) * Math.PI / 180;
    let endAngle = angle * Math.PI / 180;
    context.beginPath();
    context.moveTo(x, y);
    context.arc(x, y, radius, startAngle, endAngle, counterClockwise);
    context.closePath();
    let gradient = context.createRadialGradient(x, y, 0, x, y, radius);
    gradient.addColorStop(0, "hsl(" + angle + ", 10%, 100%)");
    gradient.addColorStop(1, "hsl(" + angle + ", 100%, 50%)");
    context.fillStyle = gradient;
    context.fill();
  }
}
</script>