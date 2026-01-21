<script setup lang="ts">
import { usePartyStore, useUIStore } from '@/stores'
import MemberRow from '@/components/timeline/MemberRow.vue'

const partyStore = usePartyStore()
const uiStore = useUIStore()
</script>

<template>
  <!-- 通常モード時のみメンバー行を表示 -->
  <div v-if="uiStore.isNormalMode" class="overflow-y-auto" style="max-height: calc(100vh - 180px)">
    <MemberRow
      v-for="member in partyStore.members"
      :key="member.id"
      :member="member"
    />
  </div>
</template>
