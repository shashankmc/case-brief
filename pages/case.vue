<template>
  <div class="lb-page page-case">
    <div class="page-header">
      <div class="eyebrow">compliance · step 1</div>
      <h1 class="serif page-title">Case File</h1>
      <p class="page-sub mono">Read the brief, then load the document pack and advance to triage.</p>
    </div>

    <CaseBrief :scenario-id="scenarioId" @output="handleOutput" />

    <div class="case-actions">
      <NuxtLink to="/list" class="case-link">Load documents →</NuxtLink>
      <NuxtLink to="/annotate" class="case-advance">Advance to Triage →</NuxtLink>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import CaseBrief from '../components/CaseBrief.vue'
import { useArtifact } from '@legalblocks/module-runtime'

// Documents themselves are handled by the reused `document-management` module
// (its own screen, route /list, emitting document-set). This screen
// only frames the case.
const scenarioId = ref('maritime-msp-deme')

// @output hands the case_brief artifact to the bus; the adapter (localStorage or
// an HTTP endpoint) is resolved from config so downstream screens can read it.
const caseBrief = useArtifact('case-brief')
const handleOutput = (artifact) => {
  caseBrief.write(artifact).catch(() => {})
}
</script>

<style scoped>
.page-case { max-width: 820px; margin: 0 auto; padding: 36px 24px; }
.page-header { margin-bottom: 24px; }
.page-title { font-size: 28px; font-weight: 400; margin: 6px 0 0; color: var(--ink); }
.page-sub { font-size: 11px; color: var(--muted); margin: 8px 0 0; }
.case-actions { display: flex; justify-content: flex-end; gap: 12px; margin-top: 24px; }
.case-link {
  background: var(--white); color: var(--ink-2); text-decoration: none; border: 1px solid var(--line);
  font-weight: 600; font-size: 13px; padding: 9px 18px; border-radius: 8px; transition: border-color 0.15s;
}
.case-link:hover { border-color: var(--vermillion); }
.case-advance {
  background: var(--vermillion); color: #fff; text-decoration: none;
  font-weight: 600; font-size: 13px; padding: 9px 18px; border-radius: 8px; transition: background 0.15s;
}
.case-advance:hover { background: var(--vermillion-2); }
</style>
