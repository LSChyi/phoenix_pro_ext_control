<script setup>
import { ref } from 'vue'

const color = ref(0);
const onEffect = ref('file_flickering');

const props = defineProps({
  part: {
    type: String,
    required: true
  },
  offset: {
    type: Number,
    required: true
  },
  device: {
    required: true
  }
})

function hexToRgb(hex) {
  var result = /^#?([a-f\d]{2})([a-f\d]{2})([a-f\d]{2})$/i.exec(hex);
  return result ? {
    r: parseInt(result[1], 16),
    g: parseInt(result[2], 16),
    b: parseInt(result[3], 16)
  } : null;
}

function changeColor() {
  let colorObj = hexToRgb(color.value)
  let dataToWrite = new Uint8Array(32);
  dataToWrite[0] = 0x07;
  dataToWrite[1] = 0x01;
  dataToWrite[2] = 0x00;
  dataToWrite[3] = props.offset;
  dataToWrite[4] = colorObj.r;
  dataToWrite[5] = colorObj.g;
  dataToWrite[6] = colorObj.b;
  props.device.sendReport(0, dataToWrite);
}

function changeOnEffect() {
  let dataToWrite = new Uint8Array(32);
  dataToWrite[0] = 0x07;
  dataToWrite[1] = 0x01;
  dataToWrite[2] = 0x01;
  dataToWrite[3] = props.offset;
  switch (onEffect.value) {
  case 'breathing':
    dataToWrite[4] = 0x04;
    break;
  case 'file_flickering':
    dataToWrite[4] = 0x05;
    break;
  }
  props.device.sendReport(0, dataToWrite);
}

function store() {
  let dataToWrite = new Uint8Array(32);
  dataToWrite[0] = 0x07;
  dataToWrite[1] = 0x01;
  dataToWrite[2] = 0x02;
  dataToWrite[3] = props.offset;
  props.device.sendReport(0, dataToWrite);
}

</script>

<template>
  <div>
    <h2>{{ part }}</h2>
    <label>選取顏色</label>
    <input type="color" @change="changeColor" v-model="color" />
    <label>選取常亮特效</label>
    <select v-model="onEffect" @change="changeOnEffect">
      <option value="breathing">呼吸燈</option>
      <option value="file_flickering">火堆閃爍</option>
    </select>
    <button @click="store">儲存設定</button>
  </div>
</template>
