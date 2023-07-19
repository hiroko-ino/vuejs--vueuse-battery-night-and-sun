<script setup lang="ts">
import { computed, reactive } from 'vue'
import { useBattery } from '@vueuse/core'
import ImageBase from './assets/image/use_battery_background.png'
import Sun from './assets/image/use_battery_sun.png'
import Moon from './assets/image/use_batterry_moon.png'
import Cloud1Base from './assets/image/use_battery_cloud_01_base.png'
import Cloud1Gd1 from './assets/image/use_battery_cloud_01_gd_01.png'
import Cloud1Gd2 from './assets/image/use_battery_cloud_01_gd_02.png'
import Cloud2Base from './assets/image/use_battery_cloud_02_base.png'
import Cloud2Gd1 from './assets/image/use_battery_cloud_02_gd_01.png'
import Cloud2Gd2 from './assets/image/use_battery_cloud_02_gd_02.png'

const battery = reactive(useBattery())

const interpolateColor = (color1: string, color2: string, ratio: number) => {
  // カラーコードを16進数からRGBに変換
  const r1 = parseInt(color1.substring(1, 3), 16);
  const g1 = parseInt(color1.substring(3, 5), 16);
  const b1 = parseInt(color1.substring(5, 7), 16);

  const r2 = parseInt(color2.substring(1, 3), 16);
  const g2 = parseInt(color2.substring(3, 5), 16);
  const b2 = parseInt(color2.substring(5, 7), 16);

  // ratioに基づいてカラーコードを補間
  const r = Math.round(r1 + (r2 - r1) * ratio);
  const g = Math.round(g1 + (g2 - g1) * ratio);
  const b = Math.round(b1 + (b2 - b1) * ratio);

  // RGBを16進数に変換してカラーコードを返す
  const hexR = r.toString(16).padStart(2, '0');
  const hexG = g.toString(16).padStart(2, '0');
  const hexB = b.toString(16).padStart(2, '0');
  return `#${hexR}${hexG}${hexB}`;
}

const getSkyColor = (value: number) => {
  if (value <= 40) {
    const ratio = value / 40;
    return interpolateColor('#746c88', '#746c88', ratio);
  } else if (value <= 80) {
    const ratio = (value - 40) / 40;
    return interpolateColor('#746c88', '#ff974e', ratio);
  } else {
    const ratio = (value - 80) / 20;
    return interpolateColor('#ff974e', '#30a4f2', ratio);
  }
}

const getGdTopColor = (value: number) => {
  if (value <= 40) {
    const ratio = value / 40;
    return interpolateColor('#241b39', '#241b39', ratio);
  } else if (value <= 80) {
    const ratio = (value - 40) / 40;
    return interpolateColor('#241b39', '#996971', ratio);
  } else {
    const ratio = (value - 80) / 20;
    return interpolateColor('#996971', '#1687d3', ratio);
  }
}

const getGdBottomColor = (value: number) => {
  if (value <= 40) {
    const ratio = value / 40;
    return interpolateColor('#c7b5c0', '#c7b5c0', ratio);
  } else if (value <= 80) {
    const ratio = (value - 40) / 40;
    return interpolateColor('#c7b5c0', '#ffe142', ratio);
  } else {
    const ratio = (value - 80) / 20;
    return interpolateColor('#ffe142', '#a2d0ee', ratio);
  }
}

const baseColor = computed(() => getSkyColor(battery.level * 100))
const gdTopColor = computed(() => getGdTopColor(battery.level * 100))
const gdBottomColor = computed(() => getGdBottomColor(battery.level * 100))

</script>

<template>
  <div class="wrapper">
    <div class="image" :style="{ backgroundColor: baseColor }">
      <div class="gd-top1" :style="{ backgroundColor: gdTopColor, opacity: 0.2 }"></div>
      <div class="gd-top2" :style="{ backgroundColor: gdTopColor, opacity: 0.2 }"></div>
      <div class="gd-top3" :style="{ backgroundColor: gdTopColor, opacity: 0.2 }"></div>
      <div class="gd-top4" :style="{ backgroundColor: gdTopColor, opacity: 0.2 }"></div>
      <div class="gd-bottom1" :style="{ backgroundColor: gdBottomColor, opacity: 0.2 }"></div>
      <div class="gd-bottom2" :style="{ backgroundColor: gdBottomColor, opacity: 0.2 }"></div>
      <div class="gd-bottom3" :style="{ backgroundColor: gdBottomColor, opacity: 0.2 }"></div>
      <div class="gd-bottom4" :style="{ backgroundColor: gdBottomColor, opacity: 0.2 }"></div>
      <img class="image__base" :src="ImageBase" alt="">
      <img :src="Sun" alt="" :style="{ position: 'absolute', top: `calc(4% + ${100 - battery.level * 100} * 3px)`, right: '10%' }">
      <img :src="Moon" alt="" :style="{ position: 'absolute', top: `calc(58% - ${100 - battery.level * 100} * 2.6px)`, right: '10%', }">
      <div class="cloud" :style="{ position: 'absolute', top: '5%', left: '7%', }">
        <img class="cloud__base" :src="Cloud1Base" alt="">
        <img class="cloud__gd1" :src="Cloud1Gd1" alt="">
        <img class="cloud__gd2" :src="Cloud1Gd2" alt="">
      </div>
      <div class="cloud" :style="{ position: 'absolute', top: '24%', left: '25%', }">
        <img class="cloud__base" :src="Cloud2Base" alt="">
        <img class="cloud__gd1" :src="Cloud2Gd1" alt="">
        <img class="cloud__gd2" :src="Cloud2Gd2" alt="">
      </div>
    </div>
  </div>
  <div class="info">
    Charging: {{ battery.charging }}<br>
    Battery label: {{ battery.level }}<br>
    Charging Time:  {{ battery.chargingTime }}<br>
    DischargingTime: {{ battery.dischargingTime }}
  </div>
</template>

<style scoped>
.wrapper {
  text-align: center;
  padding-top: 20px;
}
.image {
  display: inline-block;
  position: relative;
  height: 500px;
  overflow: hidden;
}

.image__base {
  position: relative;
  z-index: 10;
}

.cloud {
  position: relative;
}

.cloud__base, .cloud__gd1, .cloud__gd2 {
  position: absolute;
  top: 0;
  left: 0;
}

.gd-bottom1 {
  position: absolute;
  bottom: 40%;
  left: 0;
  width: 100%;
  height: 27%;
}

.gd-bottom2 {
  position: absolute;
  bottom: 40%;
  left: 0;
  width: 100%;
  height: 22%;
}

.gd-bottom3 {
  position: absolute;
  bottom: 40%;
  left: 0;
  width: 100%;
  height: 16%;
}

.gd-bottom4 {
  position: absolute;
  bottom: 40%;
  left: 0;
  width: 100%;
  height: 12%;
}

.gd-top1 {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 20%;
}

.gd-top2 {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 16%;
}

.gd-top3 {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 12%;
}

.info {
  text-align: center;
  font-family: 'DotGothic16', sans-serif;
}

</style>
