<script setup lang="ts">
import { defineProps, defineEmits, computed } from 'vue'

interface TreeNodeData {
  id: string
  title: string
  parent_id: string | null
  children?: TreeNodeData[]
}

const props = defineProps<{
  item: TreeNodeData
  openedNodes: Set<string>
  level?: number
}>()

const emit = defineEmits(['toggle'])

const level = props.level || 0

const isOpened = computed(() => props.openedNodes.has(props.item.id))

const toggle = () => {
  emit('toggle', props.item.id)
}
</script>

<template>
  <div :style="{ background: isOpened ? '#e0e0e0' : '#f0f0f0' }">
    <div @click="toggle" :style="{ marginLeft: `${level * 20}px` }" class="tree-item">
      <img src="../model/arrow-down.png" alt="arrow-down" />
      <p>{{ item.title }}</p>
    </div>
    <div v-if="isOpened">
      <TreeNode
        v-for="child in item.children"
        :key="child.id"
        :item="child"
        :openedNodes="openedNodes"
        @toggle="$emit('toggle', $event)"
        :level="level + 1"
      />
    </div>
  </div>
</template>
