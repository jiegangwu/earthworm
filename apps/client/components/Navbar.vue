<template>
  <header
    class="w-full px-5 font-customFont transition-all duration-300 ease-linear"
    :class="{
      'sticky top-0 z-10': isStickyNavBar,
      'glass bg-gradient-to-r from-transparent via-white/10 to-transparent shadow-md':
        isStickyNavBar && isScrolled,
    }"
  >
    <div class="mx-auto max-w-screen-xl">
      <div class="flex h-16 items-center justify-between">
        <div class="flex flex-1 items-center justify-between">
          <NuxtLink to="/">
            <div class="logo flex items-center">
              <img
                width="48"
                height="48"
                class="mr-6 hidden overflow-hidden rounded-md md:block"
                src="/logo.png"
                alt="earth-worm-logo"
              />
              <h1 class="text-wrap text-2xl font-extrabold leading-normal dark:text-white">
                Earthworm
              </h1>
            </div>
          </NuxtLink>

          <nav
            v-if="route.path === '/' && !isAuthenticated()"
            aria-label="Global"
            class="hidden md:block"
          >
            <ul class="flex items-center text-base">
              <li
                class="px-4"
                v-for="(optItem, optIndex) in HEADER_OPTIONS"
                :key="optIndex"
              >
                <a
                  class="text-nowrap hover:text-purple-600 dark:text-white dark:hover:text-purple-400"
                  :href="`#${optItem.anchor}`"
                >
                  {{ optItem.name }}
                </a>
              </li>
            </ul>
          </nav>
        </div>

        <div class="flex items-center">
          <!-- 显示用户信息 -->
          <div
            v-if="isAuthenticated()"
            class="logged-in flex items-center"
          >
            <div class="font-500 mx-2 max-w-[4em] truncate min-[500px]:max-w-[6em]">
              {{ userStore.userInfo?.username }}
            </div>
            <DropMenu @update-show-modal="handleLogout" />
          </div>

          <!-- 登录/注册 -->
          <button
            v-else
            aria-label="Login"
            class="btn btn-sm mr-1 border-none bg-purple-500 text-white shadow-md hover:bg-purple-600 focus:outline-none"
            @click="signIn()"
          >
            登录
          </button>

          <!-- 切换主题 -->
          <!-- -mr-1 是为了和主体内容按钮/其他元素做右对齐 -->
          <button
            class="btn btn-ghost btn-sm -mr-1 ml-1 h-8 w-8 rounded-md p-0"
            @click="toggleDarkMode"
          >
            <span
              class="h-6 w-6"
              :class="isDarkMode ? 'i-ph-moon' : 'i-ph-sun'"
            ></span>
          </button>
        </div>
      </div>
    </div>
  </header>
  <MainMessageBox
    v-model:isShowModal="isShowModal"
    title="提示"
    content="是否确认退出登录？"
    @confirm="signOut()"
  />
</template>

<script setup lang="ts">
import { useWindowScroll } from "@vueuse/core";
import { computed, ref } from "vue";
import { useRoute } from "vue-router";

import { Theme, useDarkMode } from "~/composables/darkMode";
import { isAuthenticated, signIn, signOut } from "~/services/auth";
import { useUserStore } from "~/store/user";

const route = useRoute();
const userStore = useUserStore();
const { y } = useWindowScroll();
const { darkMode, toggleDarkMode } = useDarkMode();

const SCROLL_THRESHOLD = 8;
const isShowModal = ref(false);
const HEADER_OPTIONS = [
  { name: "功能", anchor: "features" },
  { name: "问题", anchor: "faq" },
  { name: "联系我们", anchor: "contact" },
];

const isDarkMode = computed(() => darkMode.value === Theme.DARK);
const isStickyNavBar = computed(() => ["index", "User-Setting"].includes(route.name as string));
const isScrolled = computed(() => y.value >= SCROLL_THRESHOLD);

function handleLogout() {
  isShowModal.value = true;
}
</script>
