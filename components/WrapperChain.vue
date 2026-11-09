<script setup lang="ts">
import { computed } from 'vue'

/**
 * The TAF reference to an operational train number, as the XSD nests it and as it lands in the ontology.
 * Step 0: the XSD containers. Step 1: the five ontology terms they become. Step 2: what a reader needs.
 */
const props = withDefaults(defineProps<{ step?: number }>(), { step: 0 })
const s = computed(() => Math.max(0, Math.min(2, props.step)))

const xsd = ['<ReferenceOTN>', '<OperationalTrainNumberIdentifier>', '<OperationalTrainNumber>']
const owl = [
  { t: 'era:referenceOTN', k: 'link', d: 'no definition' },
  { t: 'era:ReferenceOTN', k: 'class', d: 'no definition' },
  { t: 'era:operationalTrainNumberIdentifier', k: 'link', d: 'no definition' },
  { t: 'era:OperationalTrainNumberIdentifier', k: 'class', d: 'no definition' },
  { t: 'era:operationalTrainNumber', k: 'value', d: '"12345"' },
]
</script>

<template>
  <div class="wc">
    <div class="col">
      <div class="hd">In the TAF XSD</div>
      <div v-for="(x, i) in xsd" :key="x" class="xml" :style="{ marginLeft: (i * 22) + 'px' }"><code>{{ x }}</code></div>
      <div class="note">three nested containers, one value</div>
    </div>
    <div class="col" :class="{ gone: s < 1 }">
      <div class="hd">In the ontology</div>
      <div class="chain">
        <div v-for="o in owl" :key="o.t" class="node" :class="o.k">
          <code>{{ o.t }}</code><span>{{ o.k }} · {{ o.d }}</span>
        </div>
      </div>
    </div>
    <div class="col" :class="{ gone: s < 2 }">
      <div class="hd">What a reader needs</div>
      <div class="want">
        <code>era:referenceOTN</code>
        <span>label "reference operational train number", altLabel "ROTN", a definition (the legacy UIC-based train number still used to identify national timetable sections), and ideally fewer hops to the number itself</span>
      </div>
      <div class="note">The only definition we found is in the RNE TIS documentation, not in the TSI or the XSD.</div>
    </div>
  </div>
</template>

<style scoped>
.wc { display: grid; grid-template-columns: 1fr 1.15fr 1fr; gap: 18px; margin-top: 6px; }
.col { transition: opacity .5s; }
.col.gone { opacity: 0; }
.hd { font-weight: 700; color: var(--c-heading); margin-bottom: 8px; font-size: 15px; }
.xml { background: var(--c-surface); border: 1px solid var(--c-stroke); border-radius: 6px; padding: 4px 8px; margin-bottom: 6px; display: inline-block; }
.xml code { font-size: 12px; }
.note { font-size: 12px; color: var(--c-ink-mod); margin-top: 8px; line-height: 1.3; }
.chain { display: flex; flex-direction: column; gap: 4px; }
.node { border-left: 4px solid var(--c-amber); background: var(--c-amber-bg); border-radius: 5px; padding: 3px 8px; line-height: 1.2; }
.node.value { border-left-color: var(--c-green); background: var(--c-green-bg); }
.node code { font-size: 11.5px; display: block; }
.node span { font-size: 10.5px; color: var(--c-ink-mod); }
.want { border-left: 4px solid var(--c-blue); background: var(--c-blue-bg); border-radius: 6px; padding: 8px 10px; line-height: 1.3; }
.want code { font-size: 12px; display: block; margin-bottom: 4px; }
.want span { font-size: 12.5px; }
</style>
