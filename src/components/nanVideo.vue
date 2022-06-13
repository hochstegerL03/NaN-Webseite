<template>
  <div class="container">
    <div class="position-relative">
      <div class="position-absolute">
        <div
          style="z-index: 2147483647; height: 100%; width: 100%; --bs-bg-opacity: 0.5"
          class="position-absolute hidden container-fluid d-none bg-black"
          id="menu"
        >
          <div class="row h-25">
            <div class="col-md-11 col-9 text-center"></div>
            <div class="col-md-1 col-3 text-center">
              <button
                class="btn text-white mt-3"
                style="background-color: transparent"
                @click="goBack"
              >
                X
              </button>
            </div>
          </div>
          <div class="row h-75">
            <div class="col-6 text-center my-auto">
              <button
                class="btn text-white p-3"
                style="background-color: transparent"
                @click="goNext(0)"
              >
                {{ choiceListText[counter][0] }}
              </button>
            </div>
            <div class="col-6 text-center my-auto">
              <button
                class="btn text-white p-3"
                style="background-color: transparent"
                @click="goNext(1)"
              >
                {{ choiceListText[counter][1] }}
              </button>
            </div>
          </div>
        </div>
        <div class="justify-content-center text-center" style="top: 0px" id="videoDiv">
          <video
            style="width: 100%"
            controls
            :onended="choiceMenu"
            muted="muted"
            class=""
            id="main"
          >
            <source src="/videos/seq_1.mp4" type="video/mp4" />
          </video>
          <video style="width: 100%" loop class="d-none" id="loop">
            <source src="/videos/loop.mp4" type="video/mp4" />
          </video>
        </div>
      </div>
    </div>
  </div>
</template>

<style>
.btn:hover {
  background-color: rgba(255, 255, 255, 0.05) !important;
}
</style>

<script setup>
import { onMounted, ref } from 'vue';
const video = ref();
const loop = ref();
const menu = ref();
const source = ref();
const videoDiv = ref();
const choiceList = ref([
  ['seq_1.mp4', 'seq_1.mp4', 1, 1],
  ['seq_2.mp4', 'seq_3.mp4', 0, 2],
  ['seq_4.mp4', 'seq_8.mp4', 3, 0],
  ['seq_6.mp4', 'seq_7.mp4', 0, 0],
]);
const choiceListText = ref([
  ['Zum Anfang zurückgehen.', 'Zum Anfang zurückspringen.'],
  ['Hubert beschuldigen der Mörder zu sein.', 'Ins Wohnzimmer gehen'],
  ['Leiche untersuchen.', 'Behaupten es war ein natürlicher Tod.'],
  ['Rechts vorwerfen der Mörder zu sein.', 'Schweigen.'],
  ['Auf Meinung behaaren.', 'Fragen wo Hubert war.'],
  ['Verleugnen.', 'Hubert erneut als Mörder beschimpfen.'],
]);
const counter = ref(1);
const isReady = ref([false, false]);
const choiceMenu = async () => {
  if (document.fullscreenElement) {
    if (document.exitFullscreen) document.exitFullscreen();
    else if (document.webkitExitFullscreen) document.webkitExitFullscreen();
    else if (document.mozCancelFullScreen) document.mozCancelFullScreen();
    else if (document.msExitFullscreen) document.msExitFullscreen();
  }
  menu.value.classList.toggle('d-none');
  await waitForReady(loop.value);
  loop.value.play();
  loop.value.classList.toggle('d-none');
  video.value.classList.toggle('d-none');

  // setTimeout(() => {
  //   loop.value.play();
  //   loop.value.classList.toggle('d-none');
  //   video.value.classList.toggle('d-none');
  // }, 200);
};

const goBack = async () => {
  menu.value.classList.toggle('d-none');
  await waitForReady(video.value);
  video.value.play();
  video.value.classList.toggle('d-none');
  loop.value.classList.toggle('d-none');
  // setTimeout(() => {
  //   video.value.play();
  //   video.value.classList.toggle('d-none');
  //   loop.value.classList.toggle('d-none');
  // }, 200);
};

const waitForReady = async (obj) => {
  let toCheck;
  if (obj.id === 'main') toCheck = 0;
  else if (obj.id === 'loop') toCheck = 1;
  else return;
  console.log(isReady.value[toCheck]);
  if (isReady.value[toCheck]) return;
  else {
    setTimeout(() => {
      waitForReady(obj);
    }, 200);
  }
};

const goNext = async (val) => {
  if (val == 0) video.value.src = `/videos/${choiceList.value[counter.value][val]}`;
  else video.value.src = `/videos/${choiceList.value[counter.value][val]}`;
  goBack();
  if (counter.value < choiceList.value.length - 1)
    counter.value = choiceList.value[counter.value][2 + val];
  else counter.value = 0;
};

onMounted(async () => {
  video.value = document.getElementsByTagName('video')[0];
  menu.value = document.getElementById('menu');
  source.value = document.getElementById('vidSource');
  loop.value = document.getElementsByTagName('video')[1];
  videoDiv.value = document.getElementById('videoDiv');
  video.value.load();
  video.value.play();
  video.value.addEventListener('loadeddata', () => {
    isReady.value[0] = true;
  });
  loop.value.addEventListener('loadeddata', () => {
    isReady.value[1] = true;
  });
  video.value.addEventListener('loadedmetadata', (e) => {
    let player = e.target;
    player.width = videoDiv.value.clientWidth;
    player.height = videoDiv.value.clientHeight;
  });
  loop.value.addEventListener('loadedmetadata', () => {});
});
</script>

<style></style>
