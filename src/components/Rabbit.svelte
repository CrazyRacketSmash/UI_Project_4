<script>
  import { createEventDispatcher, onMount } from 'svelte';
  const dispatch = createEventDispatcher();

  let notes = '';
  let saved = false;

  onMount(() => {
    try { notes = localStorage.getItem('rabbit_notes') || ''; saved = !!notes; } catch(e){ notes = ''; saved = false; }
  });

  function save() {
    try { localStorage.setItem('rabbit_notes', notes || ''); saved = true; } catch(e){}
    dispatch('save', notes);
  }
  function back() {
    dispatch('back');
  }
</script>

<div class="animal-page" style="padding:18px;">
  <div style="display:flex; gap:12px; align-items:center; margin-bottom:12px;">
    <div class="animal-icon" aria-hidden="true" style="width:56px; height:56px; display:flex; align-items:center; justify-content:center; border-radius:12px; background:linear-gradient(90deg,#FFD7D7,#FFB6C1);">
      <!-- rabbit svg -->
      <svg width="36" height="36" viewBox="0 0 24 24" fill="none" aria-hidden="true">
        <path d="M9 4c-.5-1-2-2-3-1s-1 3 0 4 3 1 4 0 0-2-1-3z" fill="#fff"/>
        <path d="M15 4c.5-1 2-2 3-1s1 3 0 4-3 1-4 0 0-2 1-3z" fill="#fff"/>
      </svg>
    </div>

    <div style="flex:1;">
      <div style="font-weight:700; font-size:1.05rem; margin-bottom:6px;">Rabbit — Gentle reflection</div>
      <div class="small" style="color:#444;">
        This space allows you to write down your personal concerns you have for the past few weeks
      </div>
    </div>
  </div>

  <textarea bind:value={notes} rows="10" placeholder="Write anything on your mind..." style="width:100%; padding:12px; border-radius:8px; border:1px solid rgba(15,23,42,0.06); font-size:1rem;"></textarea>

  <div style="display:flex; gap:8px; margin-top:12px; align-items:center;">
    <button class="btn btn-primary" on:click={save}>Save</button>
    <button class="btn btn-ghost" on:click={back}>Back</button>
    <div style="flex:1"></div>
    {#if saved}
      <div class="small" style="color:var(--success)">Saved</div>
    {/if}
  </div>
</div>

<style>
  .small { font-size:0.9rem; color:#6b7280; }
</style>
