<template>
  <li class="py-2">
    <div class="flex items-center group">
      <button 
        v-if="node.children?.length"
        @click="collapsed = !collapsed"
        class="w-6 h-6 mr-2 flex items-center justify-center rounded-md hover:bg-gray-100 dark:hover:bg-gray-800 transition-colors duration-200"
      >
        <span 
          class="text-gray-500 dark:text-gray-400 transform transition-transform duration-200"
          :class="{ 'rotate-90': !collapsed }"
        >
          ▸
        </span>
      </button>
      <span 
        class="text-gray-800 dark:text-gray-200 transition-colors duration-200"
        :class="{ 
          'font-semibold text-blue-600 dark:text-blue-400': matchesFilter,
          'hover:text-blue-600 dark:hover:text-blue-400': !matchesFilter
        }"
      >
        {{ node.intitule }}
      </span>
    </div>
    <ul 
      v-if="!collapsed && node.children?.length" 
      class="ml-8 border-l border-gray-200 dark:border-gray-700 pl-4 space-y-2"
    >
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
  children?: JobNode[];
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
  return node.children?.some(c => hasMatchingChildren(c)) || false;
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
  