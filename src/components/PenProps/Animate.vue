
<template>
  <div class="animate">
    <el-form @submit="(e)=>e.preventDefault()">
      <el-form-item label="动画类型">
        <el-select
          v-model="animate.animateKey"
          value-key="id"
          placeholder="选择事件类型"
          @change="changeAnimate"
        >
          <el-option
            v-for="e in m"
            :key="e.key"
            :label="e.name"
            :value="e.key"
          >
          </el-option>
        </el-select>
      </el-form-item>
      <template v-if="animate.type === 1">
        <!-- <el-form-item label="动画线宽">
          <el-input
            v-model="animate.lineWidth"
            type="number"
          />
        </el-form-item> -->
        <el-form-item label="动画颜色">
          <el-color-picker v-model="animate.animateColor " />
        </el-form-item>
        <el-form-item label="动画速度">
          <el-input
            v-model="animate.animateSpan"
            type="number"
          />
        </el-form-item>
        <el-form-item label="反向流动">
          <el-switch v-model="animate.animateReverse" />
        </el-form-item>
      </template>
      <template v-else>
        <el-form-item label="动画时长">
          <el-input
            v-model="animate.duration"
            disabled
          />
        </el-form-item>
      </template>

      <el-form-item label="自动播放">
        <el-switch v-model="animate.autoPlay" />

      </el-form-item>
      <div class="event_button">
        <el-button
          @click="startAnimate"
          type="primary"
          style="width: 60px;font-size: 10px"
        >开始动画</el-button>
        <el-button
          @click="pauseAnimate"
          type="danger"
          style="width: 60px;font-size:10px"
        >暂停动画</el-button>
        <el-button
          @click="stopAnimate"
          type="danger"
          style="width: 60px;font-size:10px"
        >停止动画</el-button>
      </div>
    </el-form>
  </div>
</template>
  
<script setup>
import { animateType } from "../../data/defaultsConfig.js";
import { onMounted, reactive, ref, toRaw } from "vue";

let m = ref(animateType);
let animate = ref({
  name: "",
  frames: [],
  key: "",
  duration: 0,
  autoPlay: false,
  animateCycle: Infinity,
});

let activePen = {};

onMounted(() => {
  meta2d.on("active", (pens) => {
    console.log("pens", pens);
    animate.value = {
      name: "",
      frames: [],
      key: "",
      duration: 0,
      autoPlay: false,
      animateCycle: Infinity,
    };
    if (pens.length === 1) {
      activePen = reactive(pens[0]);
      m.value = animateType.filter((k) => {
        return pens[0].type === 1 ? k.type === "line" : k.type === "node";
      });
    }
    if (pens[0]) {
      animate.value.type = activePen.type || 0;
      animate.value.animateKey = activePen.animateKey || "";
      animate.value.autoPlay = activePen.autoPlay || false;
      animate.value.animateCycle = activePen.animateCycle || Infinity;
      animate.value.frames = activePen.frames || [];
      animate.value.name = activePen.animateName || "";
      animate.value.duration = activePen.animateDuration || 0;
      // 线条的参数
      // animate.value.lineWidth = activePen.lineWidth || 1;
      animate.value.animateColor = activePen.animateColor || "#ff0000";
      animate.value.animateReverse = activePen.animateReverse || false;
      animate.value.lineAnimateType = activePen.lineAnimateType || 0;
      animate.value.animateSpan = activePen.animateSpan || 1;
    }
  });
});
function changeAnimate(key) {
  const fd = m.value.find((k) => key === k.key);
  console.log("fd", fd);
  if (fd) {
    animate.value.animateKey = key;
    animate.value.frames = fd.frames || [];
    animate.value.duration = fd.duration;
    // 线条的参数
    // animate.value.lineWidth = fd.lineWidth;
    animate.value.animateColor = fd.animateColor;
    animate.value.animateReverse = fd.animateReverse;
    animate.value.lineAnimateType = fd.lineAnimateType;
    animate.value.animateSpan = fd.animateSpan;
  }
}

function startAnimate() {
  activePen.animateKey = animate.value.animateKey;
  activePen.animateName = animate.value.name;
  activePen.animateDuration = animate.value.duration;
  activePen.frames = animate.value.frames;
  activePen.autoPlay = animate.value.autoPlay;
  activePen.animateCycle = animate.value.animateCycle;
  // 线条的参数
  // activePen.lineWidth = animate.value.lineWidth;
  activePen.animateColor = animate.value.animateColor;
  activePen.animateReverse = animate.value.animateReverse;
  activePen.lineAnimateType = animate.value.lineAnimateType;
  activePen.animateSpan = animate.value.animateSpan;
  meta2d.startAnimate(activePen.id);
}

function pauseAnimate() {
  meta2d.pauseAnimate(activePen.id);
}

function stopAnimate() {
  meta2d.stopAnimate(activePen.id);
}
</script>

<style scoped>
.animate {
  margin: 10px;
}

.event_button {
  width: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
  flex-wrap: wrap;
}
</style>