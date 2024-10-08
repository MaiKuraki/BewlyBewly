<script setup lang="ts">
import { Icon } from '@iconify/vue'

import { useDark } from '~/composables/useDark'
import { settings } from '~/logic'

import Tooltip from '../Tooltip.vue'
import type { HoveringDockItem } from './types'

const emit = defineEmits(['settings-visibility-change'])
const { isDark, toggleDark } = useDark()
const hideSidebar = ref<boolean>(false)

const hoveringDockItem = reactive<HoveringDockItem>({
  themeMode: false,
  settings: false,
})

onMounted(() => {
  if (settings.value.autoHideSidebar)
    hideSidebar.value = true
})

function toggleHideSidebar(hide: boolean) {
  if (settings.value.autoHideSidebar)
    hideSidebar.value = hide
  else
    hideSidebar.value = false
}
</script>

<template>
  <div
    :class="{
      'left-side': settings.sidebarPosition === 'left',
      'right-side': settings.sidebarPosition === 'right',
      'hide': hideSidebar,
    }"
    pos="fixed top-0" h-full flex items-center z-10
    pointer-events-none
  >
    <!-- Edge Div -->
    <div
      v-if="settings.autoHideSidebar && hideSidebar"
      class="sidebar-edge"
      :class="`sidebar-edge-${settings.sidebarPosition}`"
      pointer-events-auto
      @mouseenter="toggleHideSidebar(false)"
      @mouseleave="toggleHideSidebar(true)"
    />

    <div
      class="sidebar-content"
      flex="~ gap-2 col justify-center items-center"
      pointer-events-auto
      duration-300
      @mouseenter="toggleHideSidebar(false)"
      @mouseleave="toggleHideSidebar(true)"
    >
      <Tooltip :content="isDark ? $t('dock.dark_mode') : $t('dock.light_mode')" placement="left">
        <Button
          class="ctrl-btn"
          style="backdrop-filter: var(--bew-filter-glass-1);"
          center size="small" round
          @click="toggleDark"
          @mouseenter="hoveringDockItem.themeMode = true"
          @mouseleave="hoveringDockItem.themeMode = false"
        >
          <Transition name="fade">
            <div v-show="hoveringDockItem.themeMode" absolute flex>
              <Icon v-if="isDark" icon="line-md:sunny-outline-to-moon-loop-transition" />
              <Icon v-else icon="line-md:moon-alt-to-sunny-outline-loop-transition" />
            </div>
          </Transition>
          <Transition name="fade">
            <div v-show="!hoveringDockItem.themeMode" absolute flex>
              <Icon v-if="isDark" icon="line-md:sunny-outline-to-moon-transition" />
              <Icon v-else icon="line-md:moon-to-sunny-outline-transition" />
            </div>
          </Transition>
        </Button>
      </Tooltip>
      <Tooltip :content="$t('dock.settings')" placement="left">
        <Button
          class="ctrl-btn group"
          style="backdrop-filter: var(--bew-filter-glass-1);"
          center size="small" round
          @click="emit('settings-visibility-change')"
        >
          <div mt--2px>
            <i
              i-mingcute:settings-3-line w-20px h-20px group-hover:rotate-180
              transition="all 2000 ease-out"
            />
          </div>
        </Button>
      </Tooltip>
    </div>
  </div>
</template>

<style lang="scss" scoped>
.ctrl-btn {
  --b-button-width: 40px;
  --b-button-height: 40px;
  --b-button-border-width: 1px;
  --b-button-color: var(--bew-elevated);
  --b-button-color-hover: var(--bew-elevated-hover);
  --b-button-shadow: var(--bew-shadow-1);
  --b-button-shadow-hover: var(--bew-shadow-2);
  --b-button-shadow-active: var(--bew-shadow-1);

  svg {
    --uno: "w-20px h-20px shrink-0";
  }
}

.left-side {
  --uno: "left-6px";
}

.right-side {
  --uno: "right-6px";
}

.sidebar-edge {
  --uno: "absolute top-0 w-14px h-full hover:w-60px duration-300";

  &-left {
    --uno: "left-0";
  }

  &-right {
    --uno: "right-0";
  }
}

.hide.left-side .sidebar-content {
  --uno: "translate-x--100% opacity-0 pointer-events-none";
}

.hide.right-side .sidebar-content {
  --uno: "translate-x-100% opacity-0 pointer-events-none";
}
</style>
