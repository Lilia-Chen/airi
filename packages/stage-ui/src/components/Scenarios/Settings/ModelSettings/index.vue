<script setup lang="ts">
import { storeToRefs } from 'pinia'

import Live2DScene from '../../../Scenes/Live2D.vue'
import VRMScene from '../../../Scenes/VRM.vue'
import Live2D from './Live2D.vue'
import VRM from './VRM.vue'

import { useSettings } from '../../../../stores/settings'

const props = defineProps<{
  palette: string[]
  live2dSceneClass?: string | string[]
  live2dSettingsClass?: string | string[]
  vrmSceneClass?: string | string[]
  vrmSettingsClass?: string | string[]
}>()

defineEmits<{
  (e: 'extractColorsFromModel'): void
}>()

const { stageView } = storeToRefs(useSettings())
</script>

<template>
  <!-- Live2D component for 2D stage view -->
  <template v-if="stageView === '2d'">
    <div
      :class="[
        ...(props.live2dSceneClass
          ? (typeof props.live2dSceneClass === 'string' ? [props.live2dSceneClass] : props.live2dSceneClass)
          : []),
      ]"
    >
      <Live2DScene />
    </div>
    <div
      flex="~ col gap-2" :class="[
        ...(props.live2dSettingsClass
          ? (typeof props.live2dSettingsClass === 'string' ? [props.live2dSettingsClass] : props.live2dSettingsClass)
          : []),
      ]"
    >
      <Live2D :palette="palette" @extract-colors-from-model="$emit('extractColorsFromModel')" @switch-to-v-r-m="stageView = '3d'" />
    </div>
  </template>
  <!-- VRM component for 3D stage view -->
  <template v-if="stageView === '3d'">
    <div
      :class="[
        ...(props.vrmSceneClass
          ? (typeof props.vrmSceneClass === 'string' ? [props.vrmSceneClass] : props.vrmSceneClass)
          : []),
      ]"
    >
      <VRMScene />
    </div>
    <div h-full w-full p-2>
      <div
        flex="~ col gap-2"
        :class="[
          ...(props.vrmSettingsClass
            ? (typeof props.vrmSettingsClass === 'string' ? [props.vrmSettingsClass] : props.vrmSettingsClass)
            : []),
        ]"
      >
        <VRM :palette="palette" @extract-colors-from-model="$emit('extractColorsFromModel')" @switch-to-live-2-d="stageView = '2d'" />
      </div>
    </div>
  </template>
</template>
