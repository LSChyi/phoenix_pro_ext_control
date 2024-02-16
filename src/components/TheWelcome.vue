<script setup>
import Strip from './Strip.vue'

import { ref } from 'vue'

const dongleDevice = ref(null);
const reportIdx = ref(0);

function updateReportIdx() {
  const infos = dongleDevice.value.collections;
  console.log(infos);
  if (infos) {
    for (const info in infos) {
      const outputReports = infos[info].outputReports;
      if (outputReports && outputReports.length) {
        console.log("reportId is %d", outputReports[0].reportId)
        reportIdx.value = outputReports[0].reportId;
        return;
      }
    }
  }
  alert("Cannot get reportId, transaction may fail");
}

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
            updateReportIdx();
        });
      });
  } catch (err) {
    console.log(err);
    alert("Cannot connect to dongle");
  }
}
</script>

<template>
  <div v-if="dongleDevice != null">
    <Strip part="眼睛" :reportIdx="reportIdx" :offset="0" :device="dongleDevice"  />
    <Strip part="額頭" :reportIdx="reportIdx" :offset="1" :device="dongleDevice" />
    <Strip part="左胸" :reportIdx="reportIdx" :offset="2" :device="dongleDevice" />
    <Strip part="右胸" :reportIdx="reportIdx" :offset="3" :device="dongleDevice"/>
  </div>
  <div v-else>
    <button @click="connect">Connect Pro Dongle!</button>
  </div>
</template>
