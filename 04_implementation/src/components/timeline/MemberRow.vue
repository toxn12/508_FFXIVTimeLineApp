<script setup lang="ts">
import { ref, computed } from 'vue'
import type { Member } from '@/types'
import { usePartyStore, useSkillMasterStore, useUIStore } from '@/stores'
import SkillBar from '@/components/timeline-items/SkillBar.vue'
import SkillPopup from '@/components/ui/SkillPopup.vue'

const props = defineProps<{
  member: Member
}>()

const partyStore = usePartyStore()
const skillMasterStore = useSkillMasterStore()
const uiStore = useUIStore()

const showSkillPopup = ref(false)
const popupPosition = ref({ x: 0, y: 0 })
const clickedTime = ref(0)

// このメンバーのスキル配置
const skills = computed(() => props.member.skillPlacements)

// ロールカラー
const roleColor = computed(() => {
  switch (props.member.role) {
    case 'Tank': return 'border-blue-500'
    case 'Healer': return 'border-green-500'
    case 'DPS': return 'border-red-500'
    default: return 'border-gray-500'
  }
})

function handleRowClick(e: MouseEvent) {
  if (uiStore.isNormalMode) {
    const rect = (e.currentTarget as HTMLElement).getBoundingClientRect()
    popupPosition.value = {
      x: e.clientX,
      y: rect.bottom
    }
    clickedTime.value = uiStore.pixelToTime(e.clientX - rect.left)
    showSkillPopup.value = true
  }
}

function handleSkillSelect(skillId: string) {
  partyStore.addSkill(props.member.id, {
    skillId,
    time: clickedTime.value
  })
  showSkillPopup.value = false
}
</script>

<template>
  <div
    class="h-10 relative border-b border-gray-700 border-l-2"
    :class="[roleColor, uiStore.isNormalMode ? 'cursor-pointer hover:bg-gray-800/50' : '']"
    @click="handleRowClick"
  >
    <!-- スキルバー -->
    <SkillBar
      v-for="skill in skills"
      :key="skill.id"
      :placement="skill"
      :member-id="member.id"
    />

    <!-- スキル選択ポップアップ -->
    <SkillPopup
      v-if="showSkillPopup"
      :job="member.job"
      :role="member.role"
      :position="popupPosition"
      @select="handleSkillSelect"
      @close="showSkillPopup = false"
    />
  </div>
</template>
