<template>
  <titleBar />
  <navigationBar v-model:mainDivStyle="mainDivStyle" v-model:mainStyle="mainStyle" />
  <div class="mainDiv" :style="mainDivStyle">
    <div class="main" :style="mainStyle">
      <RouterView class="router-view" />
    </div>
  </div>
  <div class="routerLoading" :style="[routerLoadingS, { marginLeft: mainDivStyle.paddingLeft }]">
    <div class="loading_bg" :style="rt_loading_bgS">
      <svg xmlns="http://www.w3.org/2000/svg" class="loading" :style="rt_loadingS" width="130" height="130" viewBox="0 0 130 130">
        <circle cx="65" cy="65" r="60" stroke="#04AAEB" stroke-width="10" fill="none" class="circle" />
      </svg>
    </div>
  </div>
</template>

<script setup lang="ts">
import titleBar from './basis_c/titleBar.vue'
import navigationBar from './basis_c/navigationBar.vue'
import { RouterView, useRouter } from 'vue-router'
import { ref } from 'vue'

let ifLoadingFinish = false;
window.addEventListener('load', function () {
  ifLoadingFinish = true;
});


let mainDivStyle = ref({ paddingLeft: '50px' })
let mainStyle = ref({ width: 'calc(100vw - 50px)', position: 'static' as 'static' | 'absolute' | 'relative' | 'fixed', right: 'auto' })

let routerLoadingS = ref({ display: 'none' })
let rt_loading_bgS = ref({ width: '100px', height: '100px', opacity: 0, marginBottom: '0px', transitionDuration: '0.5s' })
let rt_loadingS = ref({ opacity: 0 })
let rt_isAnimating = false;
let Allowrouting = false;
let rt_ae_f = false

const router = useRouter()
router.beforeEach((to, from, next) => {
  if (ifLoadingFinish && rt_isAnimating == false) {
    rt_isAnimating = true;
    Allowrouting = false;
    Object.assign(rt_loading_bgS, { width: '100px', height: '100px', opacity: 0, marginBottom: '0px', transitionDuration: '0.5s' })
    routerLoadingS.value.display = 'none';
    rt_loadingS.value.opacity = 0;
    setTimeout(() => {
      routerLoadingS.value.display = 'flex';
    }, 10);
    setTimeout(() => {
      Object.assign(rt_loading_bgS.value, { width: '250px', height: '250px', opacity: 1, marginBottom: '70px' });
    }, 20);
    setTimeout(() => {
      Object.assign(rt_loading_bgS.value, { width: '200px', height: '200px', marginBottom: '0px' });
    }, 500);
    setTimeout(() => {
      rt_loadingS.value.opacity = 1;
    }, 1000);
    setTimeout(() => {
      rt_loading_bgS.value.transitionDuration = '0.75s';
    }, 1499);
    setTimeout(() => {
      Object.assign(rt_loading_bgS.value, { width: 'calc(100vw + 100vh)', height: 'calc(100vw + 100vh)' });
    }, 1500);
    setTimeout(() => {
      next()
      rt_loading_bgS.value.transitionDuration = '0.5s';
      Allowrouting = true;
    }, 2250);
  } else {
    //这里是更改路由 但好像又失效了.
    if (Allowrouting || ifLoadingFinish == false) {
      next()
      rt_ae_f = true
    }
  }
})

router.afterEach(() => {
  if (ifLoadingFinish && rt_ae_f == false) {
    Allowrouting = false;
    Object.assign(rt_loading_bgS.value, { width: '200px', height: '200px' });
    setTimeout(() => {
      rt_loadingS.value.opacity = 0;
    }, 500);
    setTimeout(() => {
      rt_loading_bgS.value.opacity = 0;
    }, 1000);
    setTimeout(() => {
      routerLoadingS.value.display = 'none';
      rt_isAnimating = false;
    }, 1500);
  } else {
    rt_ae_f = false
  }
})

</script>

<style scoped>
.titleBar {
  position: fixed;
  top: 0;
  width: 100vw;
  height: 42px;
  user-select: none;
  transition-duration: 0.3s;
  z-index: 99998;
  background-color: #F5F5F5;
}

.navigationBar {
  position: fixed;
  left: 0;
  top: 42px;
  height: calc(100vh - 42px);
  padding-top: 5px;
  user-select: none;
  transition-duration: 0.3s;
  z-index: 99997;
  background-color: #F5F5F5;
}

.mainDiv {
  width: 100vw;
  padding-top: 42px;
  overflow: hidden;
}

.main {
  height: calc(100vh - 42px);
  border-radius: 20px 0px 20px 0px;
  background: #fafafa;
  transition-duration: 0.3s;
  overflow-y: auto;
  overflow-x: hidden;
}

.router-view {
  height: 100%;
  width: 100%;
}

.routerLoading {
  width: 100%;
  height: calc(100vh - 42px);
  justify-content: center;
  align-items: center;
  position: fixed;
  bottom: 0px;
  border-radius: 20px 0px 20px 0px;
  overflow: hidden;
  transition-duration: 0.5s;
}

.routerLoading .loading_bg {
  display: flex;
  justify-content: center;
  align-items: center;
  background: #fafafa;
  border-radius: 50%;
  box-shadow: 0px 4px 10px 0px rgba(0, 0, 0, 0.3);
  flex-shrink: 0;
}

.routerLoading .loading_bg .loading {
  stroke-dasharray: 377;
  /* 半圆的圆周长（2 * π * 半径） */
  stroke-dashoffset: 70;
  animation: 1.5s linear 0s infinite normal none running routerLoading;
  transition-duration: 0.5s;
}
</style>
