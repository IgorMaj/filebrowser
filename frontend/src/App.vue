<template>
  <div>
    <router-view></router-view>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted, watch, onUnmounted } from "vue";
import { useI18n } from "vue-i18n";
import { setHtmlLocale } from "./i18n";
import { getMediaPreference, getTheme, setTheme } from "./utils/theme";
import { useLayoutStore } from "./stores/layout";

const { locale } = useI18n();

const userTheme = ref<UserTheme>(getTheme() || getMediaPreference());
const layoutStore = useLayoutStore();


function handleGlobalClick() {
  layoutStore.closeHovers()
}

onMounted(() => {
  setTheme(userTheme.value);
  setHtmlLocale(locale.value);
  document.addEventListener('click', handleGlobalClick)
  // this might be null during HMR
  const loading = document.getElementById("loading");
  loading?.classList.add("done");

  setTimeout(function () {
    loading?.parentNode?.removeChild(loading);
  }, 200);
});

onUnmounted(() => {
  document.removeEventListener('click', handleGlobalClick)
})

// handles ltr/rtl changes
watch(locale, (newValue) => {
  newValue && setHtmlLocale(newValue);
});
</script>
