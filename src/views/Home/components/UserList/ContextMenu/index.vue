<script setup lang="ts">
import { computed, inject } from 'vue'
import apis from '@/services/apis'
import { ContextMenu, ContextMenuItem, type MenuOptions } from '@imengyu/vue3-context-menu'
import { useUserStore } from '@/stores/user'
import { PowerEnum } from '@/enums'

const onAtUser = inject<(uid: number, ignore: boolean) => void>('onSelectPerson')

const props = defineProps<{
  // 消息体
  uid: number
  // 菜单设置-其它的参数透传
  options?: MenuOptions
}>()

const userInfo = useUserStore()?.userInfo
const isAdmin = computed(() => userInfo?.power === PowerEnum.ADMIN)

// 拉黑用户
const onBlockUser = async () => {
  const uid = props.uid
  if (uid) {
    await apis.blockUser({ uid }).send()
  }
}
</script>

<template>
  <ContextMenu
    :options="{
      theme: 'dark',
      x: 0,
      y: 0,
      ...props.options,
    }"
  >
    <ContextMenuItem label="艾特Ta" @click="onAtUser?.(props.uid, true)" v-login-show>
      <template #icon> <span class="icon">@</span> </template>
    </ContextMenuItem>
    <ContextMenuItem v-if="isAdmin" label="拉黑(管理)" @click="onBlockUser">
      <template #icon>
        <Icon icon="lahei" :size="13" />
      </template>
    </ContextMenuItem>
  </ContextMenu>
</template>

<style lang="scss" src="./styles.scss" />
