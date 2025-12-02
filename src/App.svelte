<script>
  import MoodPicker from './components/MoodPicker.svelte';
  import StressedBreathing from './components/stressed/Breathing.svelte';
  import StressedPMR from './components/stressed/PMR.svelte';
  import StressedTidy from './components/stressed/Tidy.svelte';

  let stage = 'picker'; // 'picker' | 'tools' | 'breath' | 'done' | 'pmr' | 'grounding' | 'meditation' | 'journal' | 'spinner'
  let mood = null;
  let breathConfig = null;

  // lightweight tools model for each mood
  const toolsByMood = {
    stressed: [
      { id: 'breath', title: 'Breathing exercise', time: '2-5 min' },
      { id: 'pmr', title: 'Progressive muscle relaxation', time: '5-10 min' },
      { id: 'tidy', title: 'Tidy 2 items', time: '5 min' }
    ],
    overwhelmed: [
      { id: 'journal', title: 'Brain dump / free-write', time: '5-15 min' },
      { id: 'prioritize', title: 'Prioritization (2 min)', time: '3-7 min' },
      { id: 'microtask_step', title: 'Tiny step micro-task', time: '10 min' },
      { id: 'pomodoro', title: 'Pomodoro mini-session', time: '10-25+ min' },
      { id: 'ask_help', title: 'Ask-for-help templates', time: '1-3 min' },
      { id: 'movement', title: 'Soothing movement (7-min)', time: '7-15 min' }
    ],
    lonely: [
      { id: 'spinner', title: 'Activity spinner', time: '5-30 min' },
      { id: 'micro_social', title: 'Micro-social prompt', time: '2-5 min' },
      { id: 'volunteer', title: 'Volunteer micro-task', time: '10-30 min' },
      { id: 'creative', title: 'Creative prompt', time: '10-30 min' },
      { id: 'comfort', title: 'Comfort list', time: '30-60 min' },
      { id: 'community', title: 'Community nudge', time: 'variable' }
    ]
  };

  // UI state
  let selectedTools = [];
  let selectedToolId = null;
  let suggestionText = '';
  $: if (mood) {
    if (mood.id === 'stressed') suggestionText = 'Try a short breathing exercise to calm your nervous system.';
    else if (mood.id === 'overwhelmed') suggestionText = 'Focus on a single small step to create space.';
    else if (mood.id === 'lonely') suggestionText = 'Try a small social or creative nudge.';
    else suggestionText = 'A short pause and breathing can help reset.';
  }

  function onChoose(selected) {
    mood = selected;
    // prepare tailored items + default breathing configs
    if (mood.id === 'stressed' || mood.id === 'overwhelmed' || mood.id === 'lonely') {
      selectedTools = toolsByMood[mood.id] || [];
      // default breath config (can be overridden when starting breath)
      if (mood.id === 'stressed') breathConfig = { cycles: 6, inhale: 4, hold: 2, exhale: 6, autoStart: false };
      else if (mood.id === 'overwhelmed') breathConfig = { cycles: 4, inhale: 4, hold: 0, exhale: 6, autoStart: false };
      else if (mood.id === 'lonely') breathConfig = { cycles: 3, inhale: 4, hold: 2, exhale: 8, autoStart: false };
      stage = 'tools';
    } else {
      selectedTools = [];
      breathConfig = null;
      stage = 'done';
    }
  }

  function backToPicker() {
    stage = 'picker';
    mood = null;
    breathConfig = null;
    selectedTools = [];
    // reset modal/tool states
    closeAllToolViews();
    selectedToolId = null;
  }

  function onFinished() {
    stage = 'done';
    closeAllToolViews();
  }

  // helper to close all special views
  function closeAllToolViews() {
    showPMR = false;
    pmrRunning = false;
    showGrounding = false;
    showMeditation = false;
    showJournal = false;
    showSpinner = false;
    audioPaused = true;
  }

  // select tool for right-hand pane (keeps user in tools view)
  function selectTool(tool) {
    // apply any immediate config changes needed before rendering the pane
    if (tool.id === 'breath') {
      breathConfig = { ...breathConfig, autoStart: true };
    } else if (tool.id === 'pmr') {
      // init pmr
      pmrIndex = 0;
      pmrRemaining = pmrSteps[pmrIndex].secs;
    } else if (tool.id === 'meditation') {
      audioSrc = 'https://cdn.simpleplaceholder.org/meditation-sample.mp3';
      audioProgress = 0;
      audioPaused = true;
    }
    selectedToolId = tool.id;
  }

  // Progressive Muscle Relaxation (PMR)
  let showPMR = false;
  let pmrSteps = [
    { label: 'Clench your fists', secs: 8 },
    { label: 'Tighten forearms', secs: 8 },
    { label: 'Tighten shoulders', secs: 8 },
    { label: 'Squeeze abdominal muscles', secs: 8 },
    { label: 'Tighten thighs', secs: 8 },
    { label: 'Tighten calves', secs: 8 },
    { label: 'Relax all', secs: 10 }
  ];
  let pmrIndex = 0;
  let pmrRemaining = 0;
  let pmrRunning = false;

  // --- Grounding 5-4-3-2-1 ---
  let showGrounding = false;
  let groundingPrompts = [
    { title: 'See 5', prompt: 'Name 5 things you can see.' },
    { title: 'Hear 4', prompt: 'Name 4 things you can hear.' },
    { title: 'Touch 3', prompt: 'Name 3 things you can feel.' },
    { title: 'Smell 2', prompt: 'Name 2 things you can smell.' },
    { title: 'Taste 1', prompt: 'Name 1 thing you can taste.' }
  ];
  let groundingIndex = 0;

  function advanceGrounding() {
    groundingIndex++;
    if (groundingIndex >= groundingPrompts.length) {
      showGrounding = false;
      stage = 'done';
    }
  }

  // --- Meditation audio player (minimal) ---
  let showMeditation = false;
  let audioSrc = ''; // replace with actual asset path when available
  let audioEl;
  let audioPaused = true;
  let audioProgress = 0;

  function toggleAudio() {
    if (!audioEl) return;
    if (audioEl.paused) { audioEl.play(); audioPaused = false; }
    else { audioEl.pause(); audioPaused = true; }
  }
  function onAudioTime() {
    if (!audioEl || !audioEl.duration) return;
    audioProgress = (audioEl.currentTime / audioEl.duration) * 100;
  }
  function stopAudioAndDone() {
    if (audioEl) { audioEl.pause(); audioEl.currentTime = 0; }
    showMeditation = false;
    stage = 'done';
  }

  // --- Journaling modal with timer ---
  let showJournal = false;
  let journalText = '';
  let journalSeconds = 0;
  let journalTimerId = null;

  function saveJournal() {
    const k = `journal_${Date.now()}`;
    localStorage.setItem(k, journalText);
    localStorage.removeItem('draft_journal');
    showJournal = false;
    if (journalTimerId) clearInterval(journalTimerId);
    stage = 'done';
  }
  function closeJournal() {
    showJournal = false;
    if (journalTimerId) clearInterval(journalTimerId);
  }

  // --- Activity spinner for Lonely ---
  let showSpinner = false;
  const spinnerActivities = [
    'Take a short photo walk (15 min)',
    'Write a 6-line poem',
    'Send a micro-social message to one person',
    'Do a small creative prompt (draw 1 minute)',
    'Make a comfort snack',
    'Browse an online forum and comment once'
  ];
  let spinnerResult = null;

  function spinOnce() {
    const idx = Math.floor(Math.random() * spinnerActivities.length);
    spinnerResult = spinnerActivities[idx];
  }

  // archived items shown in right column (merge from multiple tidy runs)
  let archivedItems = [];

  // load archived on mount
  if (typeof window !== 'undefined') {
    try {
      archivedItems = JSON.parse(localStorage.getItem('tidy_archived') || '[]');
    } catch (e) { archivedItems = []; }
  }

  function handleArchived(e) {
    // event.detail will be array of archived items from Tidy component
    const newItems = e?.detail || [];
    if (!newItems.length) return;
    archivedItems = archivedItems.concat(newItems.map(it => ({ ...it, archivedAt: Date.now() })));
    try {
      localStorage.setItem('tidy_archived', JSON.stringify(archivedItems));
    } catch (err) {}
  }

  function removeArchived(i) {
    archivedItems = archivedItems.slice(0, i).concat(archivedItems.slice(i + 1));
    try { localStorage.setItem('tidy_archived', JSON.stringify(archivedItems)); } catch(e){}
  }

  function clearArchived() {
    archivedItems = [];
    try { localStorage.removeItem('tidy_archived'); } catch(e){}
  }

</script>

<div class="app">
  <div class="container" role="main" aria-labelledby="main-title">
    <div class="header">
      <h1 id="main-title" class="h-title">MindLink — Guided Calm</h1>
      <p class="h-sub">Quick check-in & short grounding tools for stressful moments.</p>
    </div>

    {#if stage === 'picker'}
      <MoodPicker on:choose={(e) => onChoose(e.detail)} />
    {:else if stage === 'tools'}
      <!-- three-column layout: left list, center interactive pane, right archived pane -->
      <div class="tools-layout">
        <aside class="tools-sidebar">
          <div style="padding:12px 8px;">
            <div style="font-weight:600; margin-bottom:6px">{mood.label} — options</div>
            <div class="small" style="color:#666; margin-bottom:10px">{suggestionText}</div>
            <!-- svelte-ignore a11y-no-static-element-interactions -->
            {#each selectedTools as t}
              <!-- svelte-ignore a11y-click-events-have-key-events -->
              <div class="tool-card" class:selected={selectedToolId === t.id} on:click={() => selectTool(t)} style="cursor:pointer; display:flex; justify-content:space-between; align-items:center; padding:10px; border-radius:8px; margin-bottom:8px;">
                <div>
                  <div style="font-weight:600">{t.title}</div>
                </div>
                <div style="text-align:right">
                  <div class="small" style="color:#777">{t.time}</div>
                </div>
              </div>
            {/each}
          </div>
          <div style="padding:12px; border-top:1px solid #f0f0f0;">
            <button class="btn btn-ghost" on:click={backToPicker}>Back</button>
          </div>
        </aside>

        <main class="tools-pane">
          {#if !selectedToolId}
            <div style="padding:28px;">
              <h3 style="margin:0 0 8px 0">Pick an exercise</h3>
              <div class="small" style="color:#666">Select an option on the left to open the interactive tool here.</div>
            </div>

          {:else if selectedToolId === 'breath'}
            <div style="padding:18px; height:100%; display:flex; flex-direction:column;">
              <h3 style="margin:0 0 8px 0">Breathing exercise</h3>
              <div style="flex:1; display:flex; align-items:center; justify-content:center; padding:12px;">
                <StressedBreathing config={breathConfig} on:done={onFinished} />
              </div>
              <div style="margin-top:8px;">
                <button class="btn btn-ghost" on:click={() => { selectedToolId = null; }}>Close</button>
              </div>
            </div>
          {:else if selectedToolId === 'pmr'}
            <div style="padding:18px;">
              <StressedPMR on:done={onFinished} />
            </div>
          {:else if selectedToolId === 'tidy'}
            <div style="padding:18px;">
              <StressedTidy mood={mood} on:done={onFinished} on:archived={handleArchived} />
            </div>
           {/if}
         </main>

         {#if selectedToolId === 'tidy'}
           <aside class="archived-pane">
             <div style="padding:12px;">
               <div style="display:flex; justify-content:space-between; align-items:center; margin-bottom:8px;">
                 <div style="font-weight:600">Archived</div>
                 <div style="font-size:.85rem; color:#666">{archivedItems.length} items</div>
               </div>

               {#if archivedItems.length === 0}
                 <div class="small" style="color:#666">No archived items yet. Archive completed tidy items here.</div>
               {:else}
                 <div style="display:flex; flex-direction:column; gap:8px;">
                   {#each archivedItems as a, i}
                     <div style="padding:8px; border-radius:8px; background:#fff; border:1px solid rgba(15,23,42,0.04); display:flex; justify-content:space-between; align-items:center;">
                       <div style="flex:1; margin-right:8px;">
                         <div style="font-weight:600; font-size:0.95rem">{a.text || a.placeholder}</div>
                         <div class="small" style="color:#777;">{new Date(a.archivedAt || a.ts || Date.now()).toLocaleString()}</div>
                       </div>
                       <div style="display:flex; gap:6px;">
                         <button class="btn btn-ghost" on:click={() => removeArchived(i)}>Remove</button>
                       </div>
                     </div>
                   {/each}
                 </div>
                 <div style="margin-top:12px; display:flex; justify-content:flex-end; gap:8px;">
                   <button class="btn btn-ghost" on:click={clearArchived}>Clear all</button>
                 </div>
               {/if}
             </div>
           </aside>
         {/if}

      </div>

    {:else if stage === 'breath'}
      <div class="center">
        <p class="small" style="margin-bottom:12px">{suggestionText}</p>
        <div class="actions" style="justify-content:center; margin-top:12px;">
          <button class="btn btn-ghost" on:click={backToPicker}>Back</button>
        </div>
      </div>

    {:else if stage === 'done'}
      <div class="center">
        <h2>Nice work.</h2>
        <p class="small">You completed a short exercise. You can do another check-in anytime.</p>
        <div class="actions" style="justify-content:center; margin-top:12px;">
          <button class="btn btn-primary" on:click={backToPicker}>Start Another</button>
        </div>
      </div>
    {/if}
  </div>
</div>

<!-- PMR modal retained for non-tools flows — but main PMR is shown inline in tools pane -->

<!-- Grounding UI -->
{#if showGrounding}
  <div class="modal">
    <div class="modal-card">
      <h3>{groundingPrompts[groundingIndex].title}</h3>
      <p>{groundingPrompts[groundingIndex].prompt}</p>
      <div class="small" style="margin-top:8px">Tap Next when ready</div>
      <div style="display:flex; gap:8px; margin-top:12px;">
        <button class="btn btn-primary" on:click={advanceGrounding}>Next</button>
        <button class="btn btn-ghost" on:click={() => { showGrounding = false; stage = 'done'; }}>Close</button>
      </div>
    </div>
  </div>
{/if}

<!-- Meditation / Playlist UI -->
{#if showMeditation}
  <div class="modal">
    <div class="modal-card">
      <h3>Guided Meditation</h3>
      <div style="margin-top:8px">
        <audio bind:this={audioEl} src={audioSrc} on:timeupdate={onAudioTime} preload="auto"></audio>
        <div style="display:flex; align-items:center; gap:8px;">
          <button class="btn btn-ghost" on:click={toggleAudio}>{audioPaused ? 'Play' : 'Pause'}</button>
          <div style="flex:1; height:8px; background:#eee; border-radius:4px; overflow:hidden;">
            <div style="height:100%; width:{audioProgress}%; background:linear-gradient(90deg,#6aa6ff,#7ee3ff)"></div>
          </div>
          <button class="btn btn-primary" on:click={stopAudioAndDone}>Done</button>
        </div>
      </div>
    </div>
  </div>
{/if}

<!-- Journal Modal -->
{#if showJournal}
  <div class="modal">
    <div class="modal-card">
      <h3>Brain dump</h3>
      <div class="small" style="margin-bottom:8px">{Math.ceil(journalSeconds/60)} min remaining</div>
      <textarea rows="8" bind:value={journalText} style="width:100%"></textarea>
      <div style="display:flex; gap:8px; margin-top:8px;">
        <button class="btn btn-primary" on:click={saveJournal}>Save & Done</button>
        <button class="btn btn-ghost" on:click={closeJournal}>Close</button>
      </div>
    </div>
  </div>
{/if}

<!-- Spinner Modal -->
{#if showSpinner}
  <div class="modal">
    <div class="modal-card">
      <h3>Activity Spinner</h3>
      <div style="min-height:40px; display:flex; align-items:center; justify-content:center; margin:8px 0; font-weight:600;">
        {spinnerResult || 'Tap spin to get a suggestion'}
      </div>
      <div style="display:flex; gap:8px;">
        <button class="btn btn-primary" on:click={spinOnce}>Spin</button>
        <button class="btn btn-ghost" on:click={() => { showSpinner = false; stage = 'done'; }}>Close</button>
      </div>
    </div>
  </div>
{/if}

<style>
  /* ...existing code... (preserve app styles) ... */
  .modal { position:fixed; inset:0; display:flex; align-items:center; justify-content:center; background:rgba(0,0,0,0.35); }
  .modal-card { background:white; padding:16px; border-radius:10px; width:90%; max-width:520px; box-shadow:0 6px 20px rgba(0,0,0,0.12); }
  /* Tools two-column layout */
  .tools-layout { display:flex; gap:0; align-items:stretch; min-height:60vh; background:transparent; border-radius:8px; overflow:hidden; }
  .tools-sidebar { width:320px; border-right:1px solid #f2f4f6; background:#fff; display:flex; flex-direction:column; justify-content:space-between; }
  .tools-pane { flex:1; background:linear-gradient(180deg,#ffffff,#fbfdff); padding:0; }
  .archived-pane { width:280px; border-left:1px solid #f2f4f6; background:#fbfcff; display:flex; flex-direction:column; justify-content:flex-start; }
  .tool-card { transition:background .12s, box-shadow .12s; }
</style>
