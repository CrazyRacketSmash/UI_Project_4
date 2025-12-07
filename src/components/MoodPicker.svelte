<script>
  import { createEventDispatcher } from 'svelte';
  const dispatch = createEventDispatcher();

  const moods = [
    { id: 'calm', label: 'Calm', emoji: '🙂' },
    { id: 'stressed', label: 'Stressed', emoji: '😰' },
    { id: 'overwhelmed', label: 'Overwhelmed', emoji: '😵' },
    { id: 'sad', label: 'Sad', emoji: '😔' },
    { id: 'lonely', label: 'Lonely', emoji: '😞' },
    { id: 'okay', label: 'Okay', emoji: '😐' }
  ];

  function choose(m) {
    dispatch('choose', m);
  }
</script>

<section aria-label="Mood check-in">
  <h2 class="h-title" style="font-size:1.1rem">How are you feeling right now?</h2>
  <p class="small">Tap the mood that fits best — we'll suggest a short exercise.</p>

  <div class="mood-grid" role="list">
    {#each moods as mood}
      <!-- svelte-ignore a11y-no-interactive-element-to-noninteractive-role -->
      <button class="mood-btn" role="listitem" on:click={() => choose(mood)} aria-label={mood.label}>
        <div class="mood-icon" aria-hidden="true">{mood.emoji}</div>
        <div class="mood-label">{mood.label}</div>
      </button>
    {/each}
  </div>
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
  .image-actions { display:flex; gap:12px; margin-top:16px; justify-content:center; }
  .image-btn {
    width:64px; height:64px; border-radius:12px; border:1px solid rgba(15,23,42,0.06);
    display:inline-flex; align-items:center; justify-content:center; cursor:pointer;
    transition: transform .14s ease, box-shadow .14s ease;
    padding:8px;
    background-color: chocolate;
  }
  .image-btn:hover { transform: translateY(-4px); box-shadow: 0 10px 24px rgba(16,24,40,0.08); }
  .image-btn:active { transform: translateY(-1px) scale(.99); }
  .image-btn svg { display:block; width:42px; height:42px; }
</style>
