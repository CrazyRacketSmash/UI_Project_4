<script>
  import { createEventDispatcher } from 'svelte';
  const dispatch = createEventDispatcher();

  const moods = [
    { id: 'calm', label: 'Calm', emoji: '🙂' },
    { id: 'stressed', label: 'Stressed', emoji: '😰' },
    { id: 'overwhelmed', label: 'Overwhelmed', emoji: '😵' },
    { id: 'sad', label: 'Sad', emoji: '😔' },
    { id: 'lonely', label: 'Lonely', emoji: '😞' },
    { id: 'happy', label: 'Happy', emoji: '😊' }
  ];

  // local selection state so UI can show check & help-row
  let selectedId = null;
  let selectedMood = null;

  const helpPhrases = {
    stressed: 'de-stress',
    overwhelmed: 'create space',
    sad: 'feel better',
    lonely: 'connect'
  };

  $: helpText = selectedId ? (helpPhrases[selectedId] || 'reset') : '';

  // immediate choose for helper animals (whale/rabbit/bird)
  function choose(m) {
    dispatch('choose', m);
  }

  // select shows check + help row; Calm/Okay dispatch immediately
  function select(m) {
    selectedId = m.id;
    selectedMood = m;
    if (m.id === 'calm' || m.id === 'happy') {
      dispatch('choose', m);
    }
  }
  function confirmChoice() {
    if (selectedMood) {
      dispatch('choose', selectedMood);
      // keep selection (Parent will typically change stage)
    }
  }
</script>

<section aria-label="Mood check-in">
  <h2 class="h-title" style="font-size:1.1rem">How are you feeling right now?</h2>
  <p class="small">Tap the mood that fits best</p>

  <div class="mood-grid" role="list">
    {#each moods as mood}
      <!-- svelte-ignore a11y-no-interactive-element-to-noninteractive-role -->
      <!-- svelte-ignore a11y-role-supports-aria-props -->
      <button
        class="mood-btn"
        role="listitem"
        class:selected={selectedId === mood.id}
        on:click={() => select(mood)}
        aria-pressed={selectedId === mood.id}
        aria-label={mood.label}
      >
        <div class="mood-icon" aria-hidden="true">{mood.emoji}</div>
        <div class="mood-label">{mood.label}</div>
        {#if selectedId === mood.id}
          <span class="check-badge" aria-hidden="true">✔</span>
        {/if}
      </button>
    {/each}
  </div>
  <br>
  {#if selectedId && selectedId !== 'calm' && selectedId !== 'happy'}
    <div
      class="help-row"
      role="button"
      tabindex="0"
      aria-pressed="false"
      aria-label="Confirm help choice"
      on:click={confirmChoice}
      on:keydown={(e) => { if (e.key === 'Enter' || e.key === ' ') { e.preventDefault(); confirmChoice(); } }}
    >
    
      <div class="help-arrow" aria-hidden="true">→</div>
      <div class="help-text">Help me to {helpText}</div>
    </div>
  {/if}

</section>

<br>

<section aria-label="Animals">
  <h2 class="h-title" style="font-size:1.1rem">Try us out!</h2>
  <p class="small">Click on one of the cute animals</p>

  <div class="image-actions" role="group" aria-label="Cute helpers">
    <button class="image-btn" on:click={() => choose({ id: 'whale', label: 'Whale', emoji: '🐋' })} aria-label="Whale">
      <!-- simple whale SVG -->
      <svg width="48" height="48" viewBox="0 0 24 24" fill="none" aria-hidden="true">
        <path d="M2 12c2-2 6-3 10-3s8 1 10 3c0 0-1 4-6 6-3 1-6 1-10 0C3 18 2 14 2 12z" fill="#6AA6FF"/>
        <circle cx="7" cy="11" r="1.2" fill="#fff"/>
      </svg>
    </button>

    <button class="image-btn" on:click={() => choose({ id: 'rabbit', label: 'Rabbit', emoji: '🐰' })} aria-label="Rabbit">
      <!-- simple rabbit SVG -->
      <svg width="48" height="48" viewBox="0 0 24 24" fill="none" aria-hidden="true">
        <path d="M9 4c-.5-1-2-2-3-1s-1 3 0 4 3 1 4 0 0-2-1-3z" fill="#FFDADA"/>
        <path d="M15 4c.5-1 2-2 3-1s1 3 0 4-3 1-4 0 0-2 1-3z" fill="#FFDADA"/>
        <circle cx="12" cy="13" r="5" fill="#fff"/>
        <circle cx="11" cy="12" r="0.7" fill="#333"/>
        <circle cx="13" cy="12" r="0.7" fill="#333"/>
      </svg>
    </button>

    <button class="image-btn" on:click={() => choose({ id: 'bird', label: 'Bird', emoji: '🐦' })} aria-label="Bird">
      <!-- simple bird SVG -->
      <svg width="48" height="48" viewBox="0 0 24 24" fill="none" aria-hidden="true">
        <path d="M3 12c2-4 8-6 12-5 0 0-1 1-1 3s2 3 4 4c0 0-4 3-9 3S2 17 3 12z" fill="#FFDC73"/>
        <circle cx="9" cy="11" r="0.8" fill="#333"/>
      </svg>
    </button>
  </div>
</section>

<style>
  .image-actions { display:flex; gap:25px; margin-top:16px; justify-content:center; }
  .image-btn {
    width:75px; height:75px; border-radius:12px; border:1px solid rgba(15,23,42,0.06);
    display:inline-flex; align-items:center; justify-content:center; cursor:pointer;
    transition: transform .14s ease, box-shadow .14s ease;
    padding:8px;
    background-color: lightcoral;
  }
  .image-btn:hover { transform: translateY(-4px); box-shadow: 0 10px 24px rgba(16,24,40,0.08); }
  .image-btn:active { transform: translateY(-1px) scale(.99); }
  .image-btn svg { display:block; width:42px; height:42px; }

  .mood-btn { position: relative; }
  .mood-btn.selected {
    box-shadow: 0 10px 28px rgba(79,156,232,0.12);
    transform: translateY(-4px);
    border: 1px solid rgba(79,156,232,0.18);
  }
  .check-badge {
    position: absolute;
    right: 8px;
    top: 8px;
    background: linear-gradient(90deg,#6aa6ff,#7ee3ff);
    color: white;
    font-weight:700;
    border-radius:8px;
    padding:4px 6px;
    font-size:0.85rem;
    line-height:1;
    box-shadow: 0 6px 16px rgba(79,156,232,0.12);
  }

  .help-row {
    display:flex;
    align-items:center;
    gap:10px;
    margin-top:12px;
    padding:8px 10px;
    border-radius:8px;
    background: linear-gradient(90deg, rgba(14,165,233,0.06), rgba(126,227,255,0.02));
    border:1px solid rgba(14,165,233,0.07);
    width:100%;
    cursor: pointer;
    position: relative;
    overflow: hidden;
  }

  /* animated sweep overlay (hidden by default) */
  .help-row::before {
    content: "";
    position: absolute;
    inset: 0;
    left: -120%;
    width: 120%;
    pointer-events: none;
    background: linear-gradient(90deg,
      rgba(60, 221, 114, 0) 0%,
      rgba(126,227,255,0.25) 30%,
      rgba(255,255,255,0.06) 50%,
      rgba(126,227,255,0.25) 70%,
      rgba(126,227,255,0) 100%);
    transform: translateX(0);
  }

  /* play the slide on hover */
  .help-row:hover::before {
    animation: mp-slide 0.25s linear forwards;
  }

  @keyframes mp-slide {
    from { transform: translateX(-120%); }
    to   { transform: translateX(120%); }
  }

  .help-arrow {
    font-size:1.15rem;
    color: #0ea5e9;
    transform: translateX(0);
  }
  .help-text { font-weight:600; color:#064e6b; }
</style>
