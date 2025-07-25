<script setup lang="ts">
import { useLive2d, useSettings, useVRM } from '@proj-airi/stage-ui/stores'
import { RoundRange } from '@proj-airi/ui'
import { storeToRefs } from 'pinia'
import { computed } from 'vue'

const props = defineProps<{
  mode: 'x' | 'y' | 'z' | 'scale'
}>()

const { stageView, stageViewControlsEnabled } = storeToRefs(useSettings())
const { scale: vrmScale, modelOffset: vrmPosition, modelSize: vrmModelSize } = storeToRefs(useVRM())
const { scale: live2dScale, position: live2dPosition } = storeToRefs(useLive2d())

const viewControlsValueX = computed({
  get: () => {
    switch (stageView.value) {
      case '2d':
        return live2dPosition.value.x
      case '3d':
        return vrmPosition.value.x
      default:
        return 0
    }
  },
  set: (value) => {
    switch (stageView.value) {
      case '2d':
        live2dPosition.value.x = value
        break
      case '3d':
        vrmPosition.value.x = value
        break
      default:
        break
    }
  },
})

const viewControlsValueXMin = computed(() => {
  return stageView.value === '2d' ? -500 : -vrmModelSize.value.x - 10
})

const viewControlsValueXMax = computed(() => {
  return stageView.value === '2d' ? 500 : vrmModelSize.value.x + 10
})

const viewControlsValueY = computed({
  get: () => {
    switch (stageView.value) {
      case '2d':
        return live2dPosition.value.y
      case '3d':
        return vrmPosition.value.y
      default:
        return 0
    }
  },
  set: (value) => {
    switch (stageView.value) {
      case '2d':
        live2dPosition.value.y = value
        break
      case '3d':
        vrmPosition.value.y = value
        break
      default:
        break
    }
  },
})

const viewControlsValueYMin = computed(() => {
  return stageView.value === '2d' ? -500 : -vrmModelSize.value.y - 10
})

const viewControlsValueYMax = computed(() => {
  return stageView.value === '2d' ? 500 : vrmModelSize.value.y + 10
})

const viewControlsValueZ = computed({
  get: () => {
    switch (stageView.value) {
      case '2d':
        return 0
      case '3d':
        return vrmPosition.value.z
      default:
        return 0
    }
  },
  set: (value) => {
    switch (stageView.value) {
      case '2d':
        break
      case '3d':
        vrmPosition.value.z = value
        break
      default:
        break
    }
  },
})

const viewControlsValueZMin = computed(() => {
  return stageView.value === '2d' ? -500 : -vrmModelSize.value.z - 10
})

const viewControlsValueZMax = computed(() => {
  return stageView.value === '2d' ? 500 : vrmModelSize.value.z + 10
})

const viewControlsValueScale = computed({
  get: () => {
    if (stageView.value === '2d') {
      return live2dScale.value
    }

    return vrmScale.value
  },
  set: (value) => {
    if (stageView.value === '2d') {
      live2dScale.value = value
    }
    else {
      vrmScale.value = value
    }
  },
})

function resetOnMode() {
  switch (props.mode) {
    case 'x':
      viewControlsValueX.value = 0
      break
    case 'y':
      viewControlsValueY.value = 0
      break
    case 'z':
      viewControlsValueZ.value = 0
      break
    case 'scale':
      viewControlsValueScale.value = 1
      break
  }
}

defineExpose({
  resetOnMode,
})
</script>

<template>
  <Transition name="fade-side-pops-in">
    <div v-if="stageViewControlsEnabled">
      <Transition name="fade-side-pops-in" mode="out-in">
        <div v-if="props.mode === 'x'" relative class="[&_.round-range-tooltip]:hover:opacity-100">
          <RoundRange v-model="viewControlsValueX" :min="viewControlsValueXMin" :max="viewControlsValueXMax" :step="0.01" data-direction="vertical" h="50%" write-vertical-left />
          <div class="round-range-tooltip" top="50%" translate-y="[-50%]" absolute left-10 font-mono op-0 transition="all duration-200 ease-in-out">
            {{ viewControlsValueX.toFixed(2) }}
          </div>
        </div>
        <div v-else-if="props.mode === 'y'" relative class="[&_.round-range-tooltip]:hover:opacity-100">
          <RoundRange v-model="viewControlsValueY" :min="viewControlsValueYMin" :max="viewControlsValueYMax" :step="0.01" write-vertical-left h="50%" data-direction="vertical" />
          <div class="round-range-tooltip" top="50%" translate-y="[-50%]" absolute left-10 font-mono op-0 transition="all duration-200 ease-in-out">
            {{ viewControlsValueY.toFixed(2) }}
          </div>
        </div>
        <div v-else-if="stageView === '3d' && props.mode === 'z'" relative class="[&_.round-range-tooltip]:hover:opacity-100">
          <RoundRange v-model="viewControlsValueZ" :min="viewControlsValueZMin" :max="viewControlsValueZMax" :step="0.01" write-vertical-left h="50%" data-direction="vertical" />
          <div class="round-range-tooltip" top="50%" translate-y="[-50%]" absolute left-10 font-mono op-0 transition="all duration-200 ease-in-out">
            {{ viewControlsValueZ.toFixed(2) }}
          </div>
        </div>
        <div v-else-if="props.mode === 'scale'" relative class="[&_.round-range-tooltip]:hover:opacity-100">
          <RoundRange v-model="viewControlsValueScale" :min="0" :max="3" :step="0.0001" write-vertical-left h="50%" data-direction="vertical" />
          <div class="round-range-tooltip" top="50%" translate-y="[-50%]" absolute left-10 font-mono op-0 transition="all duration-200 ease-in-out">
            {{ viewControlsValueScale.toFixed(2) }}
          </div>
        </div>
      </Transition>
    </div>
  </Transition>
</template>

<style scoped>
.fade-side-pops-in-enter-active,
.fade-side-pops-in-leave-active {
  transition: all 0.2s ease-in-out;
}

.fade-side-pops-in-enter-from,
.fade-side-pops-in-leave-to {
  opacity: 0;
  transform: translateX(-100%) scale(0.8);
}

.fade-side-pops-in-enter-to,
.fade-side-pops-in-leave-from {
  opacity: 1;
  transform: translateX(0) scale(1);
}
</style>
