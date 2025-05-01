<template>
  <div class="p-8 space-y-6 max-w-4xl mx-auto">
    <h1 class="text-3xl font-bold text-gray-800 mb-6">Répertoire des Métiers</h1>

    <div class="space-y-4">
      <div class="relative">
        <input
          v-model="searchTerm"
          type="search"
          placeholder="Rechercher un métier…"
          class="w-full max-w-md px-4 py-3 border border-gray-300 rounded-lg shadow-sm focus:ring-2 focus:ring-blue-500 focus:border-blue-500 transition-all duration-200"
        />
        <div v-if="isLoading" class="absolute right-4 top-1/2 transform -translate-y-1/2">
          <div class="animate-spin rounded-full h-5 w-5 border-b-2 border-blue-500"></div>
        </div>
      </div>

      <div class="text-sm text-gray-500">
        <p v-if="searchTerm && filteredTree.length === 0">
          Aucun résultat pour "{{ searchTerm }}"
        </p>
        <p v-else-if="searchTerm">
          {{ filteredTree.length }} résultat(s) trouvé(s)
        </p>
        <p v-else>
          Commencez à taper pour rechercher un métier
        </p>
      </div>
    </div>

    <div v-if="filteredTree.length > 0" class="mt-6">
      <ul class="space-y-2">
        <JobTreeNode
          v-for="node in filteredTree"
          :key="node.code"
          :node="node"
          :filter="searchTerm"
        />
      </ul>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted, computed, watch } from 'vue'
import JobTreeNode from '~/components/JobTreeNode.vue'
import type { JobNode } from '~/types/job'

const rawTree = ref<JobNode[]>([])
const searchTerm = ref('')
const isLoading = ref(false)

/**
 * Transforme ta structure JSON en JobNode uniforme :
 * - code ➞ code_metier ou code_ogr
 * - intitule ➞ libelle
 * - children ➞ liste_domaine_prof ➞ liste_metier
 */
function normalizeArbo(nodes: any[]): JobNode[] {
  return nodes.map(n => ({
    code: n.code_metier,        // niveau arbo_principale
    intitule: n.libelle,
    children: Array.isArray(n.liste_domaine_prof)
      ? n.liste_domaine_prof.map((dom: any) => ({
          code: dom.code_metier,   // niveau domaine pro
          intitule: dom.libelle,
          children: Array.isArray(dom.liste_metier)
            ? dom.liste_metier.map((m: any) => ({
                code: m.code_ogr.toString(), // ou code_rome selon préférence
                intitule: m.libelle,
                children: []
              }))
            : []
        }))
      : []
  }))
}

// Chargement des données uniquement lors de la recherche
async function loadData() {
  if (rawTree.value.length > 0) return // Ne pas recharger si déjà chargé
  
  isLoading.value = true
  try {
    const res = await fetch('/data/unix_arborescence_principale_v458.json')
    const data = await res.json()
    const list = Array.isArray(data.arbo_principale) ? data.arbo_principale : []
    rawTree.value = normalizeArbo(list)
    console.log('Arbre chargé, racines :', rawTree.value.length)
  } catch (e) {
    console.error('Erreur fetch métiers :', e)
  } finally {
    isLoading.value = false
  }
}

// Écouteur sur le champ de recherche
watch(searchTerm, (newValue) => {
  if (newValue.trim().length > 0) {
    loadData()
  }
})

// Filtre récursif (toujours children: JobNode[])
function filterTree(nodes: JobNode[], term: string): JobNode[] {
  const t = term.trim().toLowerCase()
  if (!t) return []

  return nodes.reduce<JobNode[]>((acc, node) => {
    const children = filterTree(node.children || [], t)
    const isMatch = node.intitule.toLowerCase().includes(t)
    if (isMatch || children.length) {
      acc.push({ ...node, children })
    }
    return acc
  }, [])
}

const filteredTree = computed(() =>
  filterTree(rawTree.value, searchTerm.value)
)
</script>

<style scoped>
.animate-spin {
  animation: spin 1s linear infinite;
}

@keyframes spin {
  from {
    transform: rotate(0deg);
  }
  to {
    transform: rotate(360deg);
  }
}
</style>

