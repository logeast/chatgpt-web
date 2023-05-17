<script lang="ts" setup>
import { computed, nextTick, ref, watchEffect } from 'vue'
import { HoverButton, SvgIcon } from '@/components/common'
import { useAppStore, useChatStore } from '@/store'

interface Props {
  usingContext: boolean
}

interface Emit {
  (ev: 'export'): void
  (ev: 'toggleUsingContext'): void
}

defineProps<Props>()

const emit = defineEmits<Emit>()

const appStore = useAppStore()
const chatStore = useChatStore()

const collapsed = computed(() => appStore.siderCollapsed)
const currentChatHistory = computed(() => chatStore.getChatHistoryByCurrentActive)

const showSponsor = ref(false)
watchEffect(() => {
  // 2~3 小时弹出一次
  const waitTime = Math.floor(Math.random() * 3600000) + 7200000
  setInterval(() => {
    showSponsor.value = true
  }, waitTime)
})

function handleUpdateCollapsed() {
  appStore.setSiderCollapsed(!collapsed.value)
}

function onScrollToTop() {
  const scrollRef = document.querySelector('#scrollRef')
  if (scrollRef)
    nextTick(() => scrollRef.scrollTop = 0)
}

function handleExport() {
  emit('export')
}

function toggleUsingContext() {
  emit('toggleUsingContext')
}
</script>

<template>
  <header
    class="sticky top-0 left-0 right-0 z-30 border-b dark:border-neutral-800 bg-white/80 dark:bg-black/20 backdrop-blur"
  >
    <div class="relative flex items-center justify-between min-w-0 overflow-hidden h-14">
      <div class="flex items-center">
        <button
          class="flex items-center justify-center w-11 h-11"
          @click="handleUpdateCollapsed"
        >
          <SvgIcon v-if="collapsed" class="text-2xl" icon="ri:align-justify" />
          <SvgIcon v-else class="text-2xl" icon="ri:align-right" />
        </button>
      </div>
      <h1
        class="flex-1 px-4 pr-6 overflow-hidden cursor-pointer select-none text-ellipsis whitespace-nowrap"
        @dblclick="onScrollToTop"
      >
        {{ currentChatHistory?.title ?? '' }}
      </h1>
      <div class="flex items-center space-x-2">
        <HoverButton @click="toggleUsingContext">
          <span class="text-xl" :class="{ 'text-[#ED4192]': usingContext, 'text-[#a8071a]': !usingContext }">
            <SvgIcon icon="ri:chat-history-line" />
          </span>
        </HoverButton>
        <HoverButton @click="handleExport">
          <span class="text-xl text-[#4f555e] dark:text-white">
            <SvgIcon icon="ri:download-2-line" />
          </span>
        </HoverButton>
        <HoverButton @click="showSponsor = true">
          <span class="text-xl" :class="{ 'text-[#ED4192]': usingContext, 'text-[#a8071a]': !usingContext }">
            <n-icon>
              <svg
                class="w-4 opacity-90" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink"
                viewBox="0 0 512 512"
              >
                <path
                  d="M462.3 62.6C407.5 15.9 326 24.3 275.7 76.2L256 96.5l-19.7-20.3C186.1 24.3 104.5 15.9 49.7 62.6c-62.8 53.6-66.1 149.8-9.9 207.9l193.5 199.8c12.5 12.9 32.8 12.9 45.3 0l193.5-199.8c56.3-58.1 53-154.3-9.8-207.9z"
                  fill="currentColor"
                />
              </svg>
            </n-icon>
          </span>
        </HoverButton>
      </div>
    </div>
  </header>
</template>
