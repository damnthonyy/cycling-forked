<template>
  <div class="p-8 space-y-4">
    <h1 class="text-3xl font-bold">Répertoire des Métiers</h1>

    <div class="space-y-2">
      <input
        v-model="searchTerm"
        type="search"
        placeholder="Rechercher un métier…"
        class="border px-2 py-1 w-full max-w-md"
      />
      <p v-if="searchTerm && filteredTree.length === 0" class="text-gray-500">
        Aucun résultat pour “{{ searchTerm }}”
      </p>
      <p v-else-if="searchTerm" class="text-gray-500">
        {{ filteredTree.length }} résultat(s)
      </p>
    </div>

    <ul class="mt-4">
      <JobTreeNode
        v-for="node in filteredTree"
        :key="node.code"
        :node="node"
        :filter="searchTerm"
      />
    </ul>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted, computed } from 'vue'
import JobTreeNode from '~/components/JobTreeNode.vue'
import type { JobNode } from '~/types/job'

const rawTree = ref<JobNode[]>([])
const searchTerm = ref('')

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

// Chargement + normalisation du JSON
onMounted(async () => {
  try {
    const res = await fetch('/data/unix_arborescence_principale_v458.json')
    const data = await res.json()
    const list = Array.isArray(data.arbo_principale) ? data.arbo_principale : []
    rawTree.value = normalizeArbo(list)
    console.log('Arbre chargé, racines :', rawTree.value.length)
  } catch (e) {
    console.error('Erreur fetch métiers :', e)
  }
})

// Filtre récursif (toujours children: JobNode[])
function filterTree(nodes: JobNode[], term: string): JobNode[] {
  const t = term.trim().toLowerCase()
  if (!t) return nodes

  return nodes.reduce<JobNode[]>((acc, node) => {
    const children = filterTree(node.children, t)
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

