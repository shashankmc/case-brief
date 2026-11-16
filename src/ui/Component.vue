<template>
  <div class="bcb-shell">
    <div class="bcb-header">
      <span class="bcb-eyebrow">compliance · step 1 · case file</span>
      <h3 class="bcb-title">{{ scenario.title }}</h3>
      <span class="bcb-stats">role: {{ scenario.role }} · {{ packManifest.length }} expected docs</span>
    </div>

    <div class="bcb-brief">
      <span class="bcb-brief-label">Case brief</span>
      <p class="bcb-brief-text">{{ scenario.brief }}</p>
      <p class="bcb-question">{{ scenario.question }}</p>
    </div>

    <div class="bcb-pack">
      <div class="bcb-pack-head">
        <span class="bcb-eyebrow">expected document pack</span>
        <span class="bcb-badge">{{ packManifest.length }}</span>
      </div>
      <p class="bcb-pack-hint">
        Load these via the Document step. Each carries the agent it binds — the
        Member-State-vs-undertaking distinction the obligation extractor depends on.
      </p>
      <div class="bcb-doc-list">
        <div v-for="d in packManifest" :key="d.id" class="bcb-doc-row">
          <span class="bcb-kind" :class="'bcb-kind--' + d.kind">{{ d.kind }}</span>
          <span class="bcb-doc-ref">{{ d.ref }}</span>
          <span class="bcb-doc-binds">binds: {{ d.binds }}</span>
          <span class="bcb-doc-status" :class="{ 'bcb-doc-status--placeholder': d.status === 'placeholder' }">
            {{ d.status }}
          </span>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { computed, watch, onMounted } from 'vue'

// scenario_id is injected by the canvas runtime (config parameter); default keeps the
// component usable standalone. Documents are NOT handled here — they come from the
// reused `document-management` module (document-set). This module only emits
// the pedagogical framing + the curated pack manifest (with kind/binds metadata).
const props = defineProps({
  scenarioId: { type: String, default: 'maritime-msp-deme' },
})

const emit = defineEmits(['output'])

// Built-in scenarios mirror .raw/bluelab-poc/ in the wiki vault.
const SCENARIOS = {
  'maritime-msp-deme': {
    title: 'Maritime Spatial Planning — DEME',
    role: 'sustainability compliance officer',
    brief:
      'A maritime contractor (dredging / offshore wind) operating in EU marine waters. ' +
      'Its operations sit inside a Member State maritime spatial plan and extract / displace ' +
      'large volumes of sediment and aggregates.',
    question:
      'Does the company’s sustainability disclosure adequately report its marine-resource ' +
      'and seabed impacts against what CSRD/ESRS requires?',
    pack: [
      { id: 'msp-2014-89',  ref: 'Directive 2014/89/EU (Maritime Spatial Planning), Arts 4–8', kind: 'directive',  binds: 'Member State',    status: 'verified' },
      { id: 'esrs-e3',      ref: 'ESRS E3 (Reg. (EU) 2023/2772, Annex I), E3-1..E3-5',          kind: 'directive',  binds: 'the undertaking', status: 'verified' },
      { id: 'deme-ar-2024', ref: 'DEME Annual Report 2024 — water / marine + biodiversity',     kind: 'disclosure', binds: 'the company',     status: 'placeholder' },
    ],
  },
  custom: {
    title: 'Custom Scenario',
    role: 'compliance analyst',
    brief: 'No prebuilt pack. Define the compliance question and load documents via the Document step.',
    question: 'Define the compliance question for this scenario.',
    pack: [],
  },
}

const scenario = computed(() => SCENARIOS[props.scenarioId] ?? SCENARIOS.custom)
const packManifest = computed(() => scenario.value.pack ?? [])

// Emit the case_brief artifact for downstream screens (the register/eval stages read
// the expected pack + binds to score agent attribution). Schema: case-brief
// in @lawnotation/artifact-schemas once registered in Part A5.
function emitBrief() {
  emit('output', {
    port_id: 'case_brief',
    artifact_type: 'case-brief',
    schema_version: '0.1.0',
    payload: {
      scenario_id: props.scenarioId,
      title: scenario.value.title,
      role: scenario.value.role,
      brief: scenario.value.brief,
      question: scenario.value.question,
      pack_manifest: packManifest.value,
    },
    lineage: {
      produced_by_package: 'case-brief',
      produced_by_module: 'caseBrief',
    },
  })
}

watch(() => props.scenarioId, emitBrief)
onMounted(emitBrief)
</script>

<style scoped>
.bcb-shell { font-family: system-ui, sans-serif; padding: 20px; display: flex; flex-direction: column; gap: 16px; }

.bcb-eyebrow { font-family: monospace; font-size: 10px; font-weight: 500; letter-spacing: 0.14em; text-transform: uppercase; color: #8C8579; display: block; }
.bcb-title { font-size: 20px; font-weight: 600; margin: 4px 0 0; color: #1A1714; }
.bcb-stats { font-size: 11px; color: #8C8579; font-family: monospace; }

.bcb-brief { background: #F0F5F8; border: 1px solid #CFE0E8; border-left: 3px solid #2E6E8E; border-radius: 8px; padding: 14px 16px; }
.bcb-brief-label { font-family: monospace; font-size: 10px; font-weight: 600; letter-spacing: 0.1em; text-transform: uppercase; color: #2E6E8E; }
.bcb-brief-text { font-size: 14px; line-height: 1.6; color: #1A1714; margin: 6px 0 0; }
.bcb-question { font-size: 13px; font-style: italic; color: #4A453E; margin: 10px 0 0; }

.bcb-pack-head { display: flex; align-items: center; gap: 8px; margin-bottom: 6px; }
.bcb-badge { background: #2E6E8E; color: #fff; font-size: 10px; font-weight: 700; padding: 1px 7px; border-radius: 999px; }
.bcb-pack-hint { font-size: 11px; color: #8C8579; margin: 0 0 10px; line-height: 1.5; }
.bcb-doc-list { display: flex; flex-direction: column; gap: 6px; }
.bcb-doc-row {
  display: grid; grid-template-columns: 84px 1fr auto auto; gap: 12px; align-items: center;
  padding: 10px 12px; background: #fff; border: 1px solid #E5DFD3; border-radius: 8px; font-size: 13px;
}
.bcb-kind { font-family: monospace; font-size: 9px; font-weight: 600; text-transform: uppercase; letter-spacing: 0.06em; padding: 2px 6px; border-radius: 4px; text-align: center; }
.bcb-kind--directive  { background: #E7EFE2; color: #5F7A3A; }
.bcb-kind--disclosure { background: #FDE8E0; color: #B6371A; }
.bcb-kind--treaty     { background: #ECE4F2; color: #7B4F9E; }
.bcb-doc-ref { font-weight: 500; color: #1A1714; }
.bcb-doc-binds { font-family: monospace; font-size: 10px; color: #8C8579; }
.bcb-doc-status { font-family: monospace; font-size: 10px; color: #5F7A3A; }
.bcb-doc-status--placeholder { color: #B6371A; }
</style>
