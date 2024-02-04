<script setup>
import Strip from './Strip.vue'

import { ref } from 'vue'

const dongleDevice = ref(null);

function connect() {
  try {
    let deviceFilter = { usagePage: 0xff60, usage: 0x61 };
    let requestParams = { filters: [deviceFilter] };
    navigator.hid.requestDevice(requestParams)
      .then((devices) => {
        if (devices.length == 0)
          return;

        devices[0].open().then(() => {
            dongleDevice.value = devices[0];
        });
      });
  } catch (err) {
    console.log(err);
    alert("Cannot conntect to dongle");
  }
}
</script>

<template>
  <div v-if="dongleDevice != null">
    <Strip part="眼睛" :offset="0" :device="dongleDevice"  />
    <Strip part="額頭" :offset="1" :device="dongleDevice" />
    <Strip part="左胸" :offset="2" :device="dongleDevice" />
    <Strip part="右胸" :offset="3" :device="dongleDevice"/>
  </div>
  <div v-else>
    <button @click="connect">Connect Pro Dongle!</button>
  </div>
</template>
