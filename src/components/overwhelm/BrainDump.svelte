<script>
  import { createEventDispatcher, onMount, onDestroy } from 'svelte';
  const dispatch = createEventDispatcher();

  let text = '';
  let seconds = 300; // default 5 min timer
  let running = false;
  let timerId = null;

  onMount(() => {
    try { text = localStorage.getItem('brain_dump_draft') || ''; } catch(e){}
  });

  onDestroy(() => {
    if (timerId) clearInterval(timerId);
  });

  function startStop(){
    if (running){
      running = false;
      clearInterval(timerId);
      timerId = null;
    } else {
      running = true;
      timerId = setInterval(()=> {
        if (seconds<=0){ stopAndFinish(); }
        else seconds -= 1;
      },1000);
    }
  }
  function stopAndFinish(){
    if (timerId) clearInterval(timerId);
    running = false;
    timerId = null;
    saveAndDone();
  }
  function saveDraft(){
    try { localStorage.setItem('brain_dump_draft', text || ''); } catch(e){}
  }
  function saveAndDone(){
     // save the text to a timestamped entry
     const k = `brain_dump_${Date.now()}`;
     try {
       localStorage.setItem(k, text || '');
       localStorage.removeItem('brain_dump_draft');
     } catch(e){ console.error('Save failed:', e); }
     // dispatch done event to parent (App.svelte)
     dispatch('done');
  }
</script>

<div>
  <h3 style="margin:0 0 8px 0">Brain dump - Ultimate way to clear your mind</h3>
  <p class="small">Write anything freely for a few minutes to clear your mind. Start the timer when you're ready!</p>

  <textarea bind:value={text} rows="8" placeholder="Let it out..." on:input={saveDraft} style="width:100%; margin-top:8px; padding:10px;"></textarea>

  <div style="display:flex; gap:8px; align-items:center; margin-top:10px;">
    <div style="font-weight:700">{Math.floor(seconds/60)}:{String(seconds%60).padStart(2,'0')}</div>
    <button class="btn" on:click={startStop}>{running ? 'Pause' : 'Start'}</button>
    <button class="btn btn-primary" on:click={saveAndDone}>Save & Done</button>
    <div style="flex:1"></div>
    <button class="btn btn-ghost" on:click={() => dispatch('done')}>Close</button>
  </div>
</div>

<style>
  .small { font-size:0.9rem; color:#6b7280; }
  .btn { padding:8px 10px; border-radius:8px; background:#eef; border:1px solid #cde; cursor:pointer; }
  .btn-primary { background: linear-gradient(90deg,#6aa6ff,#7ee3ff); color:white; border:none; padding:8px 12px; border-radius:8px; }
  .btn-ghost { background:transparent; border:1px solid rgba(0,0,0,0.06); padding:8px 10px; border-radius:8px; }
</style>
