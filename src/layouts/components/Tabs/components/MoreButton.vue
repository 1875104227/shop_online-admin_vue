<template>
  <el-dropdown trigger="click" :teleported="false">
    <el-button size="small" type="primary">
      <span>{{ $t('tabs.more') }}</span>
      <el-icon class="el-icon--right"><arrow-down /></el-icon>
    </el-button>
    <template #dropdown>
      <el-dropdown-menu>
        <el-dropdown-item @click="refresh">
          <el-icon><Refresh /></el-icon>{{ $t('tabs.refresh') }}
        </el-dropdown-item>
        <el-dropdown-item @click="maximize">
          <el-icon><FullScreen /></el-icon>{{ $t('tabs.maximize') }}
        </el-dropdown-item>
        <el-dropdown-item divided @click="closeCurrentTab">
          <el-icon><Remove /></el-icon>{{ $t('tabs.closeCurrent') }}
        </el-dropdown-item>
        <el-dropdown-item @click="closeOtherTab">
          <el-icon><CircleClose /></el-icon>{{ $t('tabs.closeOther') }}
        </el-dropdown-item>
        <el-dropdown-item @click="closeAllTab">
          <el-icon><FolderDelete /></el-icon>{{ $t('tabs.closeAll') }}
        </el-dropdown-item>
      </el-dropdown-menu>
    </template>
  </el-dropdown>
</template>

<script setup lang="ts">
import { inject, nextTick } from 'vue'
import { useTabsStore } from '@/store/modules/tabs'
import { useAppStoreWithOut } from '@/store/modules/app'
import { useKeepAliveStore } from '@/store/modules/keepAlive'
import { useRoute, useRouter } from 'vue-router'

const route = useRoute()
const router = useRouter()
const tabStore = useTabsStore()
const appStore = useAppStoreWithOut()
const keepAliveStore = useKeepAliveStore()

// refresh current page
const refreshCurrentPage: Function = inject('refresh') as Function
const refresh = () => {
  setTimeout(() => {
    keepAliveStore.removeKeepAliveName(route.name as string)
    refreshCurrentPage(false)
    nextTick(() => {
      keepAliveStore.addKeepAliveName(route.name as string)
      refreshCurrentPage(true)
    })
  }, 0)
}

// maximize current page
const maximize = () => {
  appStore.setTheme('maximize', true)
}

// Close Current
const closeCurrentTab = () => {
  if (route.meta.isAffix) return
  tabStore.removeTabs(route.fullPath)
  keepAliveStore.removeKeepAliveName(route.name as string)
}

// Close Other
const closeOtherTab = () => {
  tabStore.closeMultipleTab(route.fullPath)
  keepAliveStore.setKeepAliveName([route.name] as string[])
}

// Close All
const closeAllTab = () => {
  tabStore.closeMultipleTab()
  keepAliveStore.setKeepAliveName()
  router.push(tabStore.tabsMenuList[0].path)
}
</script>

<style scoped lang="less">
@import '../index.less';
</style>
