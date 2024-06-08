<script setup lang="ts">
import { ref, onMounted } from 'vue'
import axios from 'axios'

import { TreeNode } from '@/features/TreeNode'

interface TreeNodeData {
  id: string
  title: string
  parent_id: string | null
  children?: TreeNodeData[]
}

const treeData = ref<TreeNodeData[]>([])
const openedNodes = ref<Set<string>>(new Set())
const rerenderCounter = ref<number>(0)

const fetchTreeData = async () => {
  try {
    const response = await axios.get('https://64b4c8450efb99d862694609.mockapi.io/tree/items')
    const data = response.data as TreeNodeData[]
    const tree = buildTree(data)
    treeData.value = tree
  } catch (error) {
    console.error('Error fetching tree data:', error)
  }
}

const buildTree = (data: TreeNodeData[]): TreeNodeData[] => {
  const idMap: { [key: string]: TreeNodeData } = {}
  data.forEach((item) => {
    idMap[item.id] = { ...item, children: [] }
  })

  const tree: TreeNodeData[] = []
  data.forEach((item) => {
    if (item.parent_id && idMap[item.parent_id]) {
      idMap[item.parent_id].children?.push(idMap[item.id])
    } else {
      tree.push(idMap[item.id])
    }
  })

  return tree
}

const toggleNode = (id: string) => {
  if (openedNodes.value.has(id)) {
    openedNodes.value.delete(id)
  } else {
    openedNodes.value.add(id)
  }
}

const rerender = () => {
  rerenderCounter.value++
}

onMounted(fetchTreeData)
</script>

<template>
  <button @click="rerender">Ререндер {{ rerenderCounter }}</button>
  <nav v-for="item in treeData" :key="item.id">
    <TreeNode :item="item" :openedNodes="openedNodes" @toggle="toggleNode" />
  </nav>
</template>
