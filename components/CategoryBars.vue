<script setup lang="ts">
import { computed } from 'vue'

/**
 * The 36 items of the xsd-improvements worklist (telematics-ontology, label xsd-improvements,
 * without the umbrella item), grouped by the pattern behind them.
 * `fixed` = a post-processing rule works around it today; the rest is unchanged in the ontology.
 * Step 0: totals. Step 1: split into "worked around in the pipeline" and "nothing done".
 */
const props = withDefaults(defineProps<{ step?: number }>(), { step: 0 })
const s = computed(() => Math.max(0, Math.min(1, props.step)))

const cats = [
  { name: 'Code lists: meanings in prose, free text, patterns', total: 8, fixed: 7, items: '#29 #30 #41 #49 #79 #107 #115 #146' },
  { name: 'One concept, several structures', total: 5, fixed: 4, items: '#26 #33 #34 #40 #103' },
  { name: 'Inconsistent or wrong datatypes', total: 5, fixed: 2, items: '#28 #36 #108 #114 #119' },
  { name: 'Wrong documentation and typos', total: 5, fixed: 1, items: '#31 #32 #35 #48 #106' },
  { name: 'Same name, different meaning', total: 4, fixed: 3, items: '#109 #112 #113 #116' },
  { name: 'Names and containers', total: 4, fixed: 1, items: '#27 #50 #117 #120' },
  { name: 'Dead or lost structure', total: 3, fixed: 2, items: '#58 #92 #105' },
  { name: 'Quantities without a unit', total: 2, fixed: 0, items: '#118 #147' },
]
const max = 8
</script>

<template>
  <div class="cb">
    <div v-for="c in cats" :key="c.name" class="row">
      <div class="lbl">{{ c.name }}<span class="ids">{{ c.items }}</span></div>
      <div class="track">
        <div class="bar" :style="{ width: (100 * c.total / max) + '%' }">
          <div class="seg ok" :class="{ on: s >= 1 }" :style="{ width: (100 * c.fixed / c.total) + '%' }" />
          <div class="seg todo" :class="{ on: s >= 1 }" :style="{ width: (100 * (c.total - c.fixed) / c.total) + '%' }" />
        </div>
        <span class="n">{{ c.total }}</span>
      </div>
    </div>
    <div class="legend" :class="{ gone: s < 1 }">
      <span><i class="sw ok" /> worked around by a pipeline rule (20)</span>
      <span><i class="sw todo" /> left as the source says it (16)</span>
    </div>
  </div>
</template>

<style scoped>
.cb { display: flex; flex-direction: column; gap: 7px; margin-top: 6px; }
.row { display: grid; grid-template-columns: 330px 1fr; gap: 12px; align-items: center; }
.lbl { font-size: 14px; line-height: 1.15; text-align: right; }
.ids { display: block; font-size: 10px; color: var(--c-ink-subtle); letter-spacing: .02em; }
.track { display: flex; align-items: center; gap: 8px; }
.bar { display: flex; height: 22px; border-radius: 5px; overflow: hidden; background: var(--c-blue); transition: all .5s; }
.seg { height: 100%; transition: background .5s; background: var(--c-blue); }
.seg.ok.on { background: var(--c-blue); }
.seg.todo.on { background: var(--c-amber); }
.n { font-weight: 700; font-size: 15px; }
.legend { display: flex; gap: 22px; justify-content: center; font-size: 13px; color: var(--c-ink-mod); margin-top: 6px; transition: opacity .5s; }
.legend.gone { opacity: 0; }
.sw { display: inline-block; width: 12px; height: 12px; border-radius: 3px; vertical-align: -1px; margin-right: 4px; }
.sw.ok { background: var(--c-blue); }
.sw.todo { background: var(--c-amber); }
</style>
