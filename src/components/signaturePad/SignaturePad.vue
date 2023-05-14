<template>
  <div>
    <canvas
        ref="canvas"
        width="550"
        height="250"
        @mousedown="mousedown"
        @mousemove="mousemove"
        @mouseup="sign = false"
        @mouseout="sign = false"
    />
    <br>
    <div class="d-flex justify-content-end mt-2">
      <b-button variant="outline-primary" @click="clear">Clear</b-button>
      <b-button variant="primary" class="mx-2" @click="done">Done</b-button>
    </div>
  </div>
</template>

<script lang="ts">
import { Component, Prop, Vue } from "vue-property-decorator";

@Component({})
export default class SignaturePad extends Vue {
  @Prop() modelValue: any;

  $refs!: {
    canvas: HTMLCanvasElement;
  };

  ctx: any = null;
  sign = false;
  prevX = 0;
  prevY = 0;

  mounted() {
    this.ctx = this.$refs.canvas.getContext("2d");
    this.ctx.strokeStyle = "black";
    this.ctx.lineWidth = 3;
  }

  mousedown($event) {
    this.sign = true;
    this.prevX = $event.offsetX;
    this.prevY = $event.offsetY;
  }

  mousemove($event) {
    if (this.sign) {
      const currX = $event.offsetX;
      const currY = $event.offsetY;
      this.draw(this.prevX, this.prevY, currX, currY);
      this.prevX = currX;
      this.prevY = currY;
    }
  }

  draw(depX, depY, destX, destY) {
    this.ctx.beginPath();
    this.ctx.moveTo(depX, depY);
    this.ctx.lineTo(destX, destY);
    this.ctx.closePath();
    this.ctx.stroke();
    this.modelValue = 'signature';
  }

  clear() {
    this.modelValue = null;
    this.ctx.clearRect(
        0,
        0,
        this.$refs.canvas.width,
        this.$refs.canvas.height
      );
  }

  done() {
    if(this.modelValue) {
      const img = this.$refs.canvas.toDataURL("image/png").replace("image/png", "image/octet-stream");
      this.$emit("onCanvasUpdated", img);
    } else {
      this.$emit("onCanvasUpdated", null);
    }
  }
}
</script>
<style lang="scss" scoped>
canvas {
  border: 1px solid black;
  background-color: white;
  cursor: crosshair;
}
</style>