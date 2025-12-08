<script>
  import { createEventDispatcher, onDestroy } from 'svelte';
  const dispatch = createEventDispatcher();

  export let pmrSteps = [
    { label: 'Clench your fists', secs: 8 },
    { label: 'Tighten forearms', secs: 8 },
    { label: 'Tighten shoulders', secs: 8 },
    { label: 'Squeeze abdominal muscles', secs: 8 },
    { label: 'Tighten thighs', secs: 8 },
    { label: 'Tighten calves', secs: 8 },
    { label: 'Relax all', secs: 10 }
  ];

  let index = 0;
  let remaining = 0;
  let running = false;
  let timerId = null;

  function startStep() {
    if (timerId) clearInterval(timerId);
    remaining = pmrSteps[index].secs;
    try { speechSynthesis.cancel(); } catch(e){}
    if ('speechSynthesis' in window) {
      const u = new SpeechSynthesisUtterance(pmrSteps[index].label);
      u.rate = 0.95;
      u.onend = () => {};
      speechSynthesis.speak(u);
    }
    running = true;
    timerId = setInterval(() => {
      remaining--;
      if (remaining <= 0) {
        clearInterval(timerId);
        timerId = null;
        index++;
        if (index >= pmrSteps.length) {
          running = false;
          dispatch('done');
        } else {
          // brief pause then next
          setTimeout(() => startStep(), 600);
        }
      }
    }, 1000);
  }

  function toggle() {
    if (running) {
      running = false;
      if (timerId) clearInterval(timerId);
      try { speechSynthesis.cancel(); } catch(e){}
    } else {
      startStep();
    }
  }

  function closeAll() {
    if (timerId) clearInterval(timerId);
    try { speechSynthesis.cancel(); } catch(e){}
    dispatch('done');
  }

  onDestroy(() => {
    if (timerId) clearInterval(timerId);
    try { speechSynthesis.cancel(); } catch(e){}
  });
</script>

<div>
  <h3 style="margin:0 0 8px 0">Progressive muscle relaxation</h3>
  <div style="margin-top:8px;">
    <div style="font-weight:600; transition:transform .18s ease;">
      {pmrSteps[index]?.label}
    </div>
    <div class="small" style="margin-top:6px">{remaining}s</div>
  </div>

  <div style="display:flex; gap:8px; margin-top:12px;">
    <button class="btn btn-ghost" on:click={toggle}>{running ? 'Pause' : 'Start'}</button>
    <button class="btn btn-primary" on:click={() => { index = pmrSteps.length; dispatch('done'); }}>Done</button>
  </div>
</div>

<style>
  .small { font-size:0.9rem; color:#6b7280; }
</style>
