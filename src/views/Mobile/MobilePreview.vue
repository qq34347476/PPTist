<template>
  <div class="mobile-preview" ref="mobileRef">
    <div class="thumbnail-list">
      <div class="thumbnail-item" v-for="(slide, index) in slides" :key="slide.id">
        <ThumbnailSlide 
          :slide="slide" 
          :size="screenWidth - 20" 
          :visible="index < slidesLoadLimit" 
        />
      </div>
    </div>
    <div class="menu">
      <div class="menu-item" @click="changeMode('editor')"><IconEdit class="icon" /> 编辑</div>
      <Divider type="vertical" style="height: 30px;" />
      <div class="menu-item" @click="changeMode('player')"><IconFullScreenPlay class="icon" /> 播放</div>
      <Divider type="vertical" style="height: 30px;" />
      <div class="menu-item ai" @click="openAIPPTDialog()">AIPPT</div>
    </div>
  </div>

  <Modal
    :visible="showAIPPTDialog" 
    :closeOnClickMask="false"
    :closeOnEsc="false"
    :width="width"
    closeButton
    @closed="closeAIPPTDialog()"
  >
    <AIPPTDialog />
  </Modal>
</template>

<script lang="ts" setup>
import { onMounted, ref, useTemplateRef } from 'vue'
import { storeToRefs } from 'pinia'
import { useMainStore, useSlidesStore } from '@/store'
import useLoadSlides from '@/hooks/useLoadSlides'
import type { Mode } from '@/types/mobile'

import ThumbnailSlide from '@/views/components/ThumbnailSlide/index.vue'
import Divider from '@/components/Divider.vue'
import Modal from '@/components/Modal.vue'
import AIPPTDialog from '../Editor/AIPPTDialog.vue'

defineProps<{
  changeMode: (mode: Mode) => void
}>()

const { slides } = storeToRefs(useSlidesStore())
const { slidesLoadLimit } = useLoadSlides()
const mainStore = useMainStore()
const { showAIPPTDialog } = storeToRefs(mainStore)

const openAIPPTDialog = () => mainStore.setAIPPTDialogState(true)
const closeAIPPTDialog = () => mainStore.setAIPPTDialogState(false)

const mobileRef = useTemplateRef<HTMLElement>('mobileRef')
const screenWidth = ref(0)
const width = ref(400)

onMounted(() => {
  if (!mobileRef.value) return
  screenWidth.value = mobileRef.value.clientWidth
  width.value = document.body.clientWidth - 20
})
</script>

<style lang="scss" scoped>
.mobile-preview {
  height: 100%;
  background-color: #f9f9f9;
}
.thumbnail-list {
  height: calc(100% - 50px);
  padding: 10px;
  overflow: auto;
}
.thumbnail-item {
  display: flex;
  justify-content: center;
  align-items: center;
  box-shadow: 0 0 15px 0 rgba(0, 0, 0, 0.1);

  & + .thumbnail-item {
    margin-top: 10px;
  }
}
.menu {
  height: 50px;
  position: relative;
  box-shadow: 0 -2px 4px 0 rgba($color: #333, $alpha: 0.05);
  background: #fff;
  display: flex;
  justify-content: center;
  align-items: center;

  .menu-item {
    width: 50%;
    height: 100%;
    display: flex;
    justify-content: center;
    align-items: center;
    font-size: 13px;

    .icon {
      margin-right: 8px;
      font-size: 18px;
    }

    &.ai {
      background: linear-gradient(270deg, #d897fd, #33bcfc);
      background-clip: text;
      color: transparent;
      font-weight: 700;
    }
  }
}
</style>
