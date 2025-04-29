<template>
    <li class="py-1">
      <div class="flex items-center">
        <button v-if="node.children.length"
                @click="collapsed = !collapsed"
                class="w-4 h-4 mr-1">
          <span v-if="collapsed">▸</span><span v-else>▾</span>
        </button>
        <span :class="{ 'font-semibold': matchesFilter }">
          {{ node.intitule }}
        </span>
      </div>
      <ul v-if="!collapsed && node.children.length" class="ml-4">
        <JobTreeNode
          v-for="child in node.children"
          :key="child.code"
          :node="child"
          :filter="filter"
        />
      </ul>
    </li>
  </template>
  
  <script setup lang="ts">
  import { ref, computed, watch } from 'vue';
  
  interface JobNode {
    code: string;
    intitule: string;
    children: JobNode[];
  }
  
  const props = defineProps<{
    node: JobNode;
    filter: string;
  }>();
  
  const collapsed = ref(false);
  
  const matchesFilter = computed(() =>
    props.filter
      ? props.node.intitule.toLowerCase().includes(props.filter.toLowerCase())
      : false
  );
  
  const hasMatchingChildren = (node: JobNode): boolean => {
    if (!props.filter) return true;
    if (node.intitule.toLowerCase().includes(props.filter.toLowerCase())) {
      return true;
    }
    return node.children.some(c => hasMatchingChildren(c));
  };
  
  watch(
    () => props.filter,
    (newFilter) => {
      if (newFilter) {
        collapsed.value = !hasMatchingChildren(props.node);
      } else {
        collapsed.value = false;
      }
    },
    { immediate: true }
  );
  </script>
  