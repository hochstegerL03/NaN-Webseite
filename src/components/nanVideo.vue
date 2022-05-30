<template>
  <div class="container">
    <div class="position-relative">
      <div class="position-absolute">
        <div
          style="z-index: 2; height: 100%; width: 100%; --bs-bg-opacity: 0.5"
          class="position-absolute hidden container-fluid d-none bg-black"
          id="menu"
        >
          <div class="row">
            <div class="col-md-11 col-9 text-center"></div>
            <div class="col-md-1 col-3 text-center">
              <button class="btn btn-primary mt-3" @click="goBack">X</button>
            </div>
          </div>
          <div class="row h-100">
            <div class="col-6 text-center my-auto">
              <button class="btn btn-primary text-center" @click="goNext(1)">Option 1</button>
            </div>
            <div class="col-6 text-center my-auto">
              <button class="btn btn-primary" @click="goNext(2)">Option 2</button>
            </div>
          </div>
        </div>
        <div class="justify-content-center text-center" style="z-index: 0; top: 0px">
          <video
            style="width: 100%"
            controls
            :onended="choiceMenu"
            muted="muted"
            class=""
            id="main"
          >
            <source src="/videos/placeholder.mp4" type="video/mp4" />
          </video>
          <video style="width: 100%" loop class="d-none" id="loop">
            <source src="/videos/loop.mp4" type="video/mp4" />
          </video>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { onMounted, ref } from 'vue';
const video = ref();
const loop = ref();
const menu = ref();
const source = ref();
const isReady = ref([false, false]);
const choiceMenu = async () => {
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
  if (val == 1) video.value.src = '/videos/next.mp4';
  else video.value.src = '/videos/2.mp4';
  goBack();
};

onMounted(async () => {
  video.value = document.getElementsByTagName('video')[0];
  menu.value = document.getElementById('menu');
  source.value = document.getElementById('vidSource');
  loop.value = document.getElementsByTagName('video')[1];
  video.value.load();
  video.value.play();
  video.value.addEventListener('loadeddata', (event) => {
    isReady.value[0] = true;
  });
  loop.value.addEventListener('loadeddata', (event) => {
    isReady.value[1] = true;
  });
});
</script>

<style></style>
