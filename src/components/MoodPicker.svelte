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
  <p class="small">Tap the mood that fits best — we’ll suggest a short exercise.</p>

  <div class="mood-grid" role="list">
    {#each moods as mood}
      <!-- svelte-ignore a11y-no-interactive-element-to-noninteractive-role -->
      <button class="mood-btn" role="listitem" on:click={() => choose(mood)} aria-label={mood.label}>
        <div class="mood-icon" aria-hidden="true">{mood.emoji}</div>
        <div class="mood-label">{mood.label}</div>
      </button>
    {/each}
  </div>

  <div class="actions">
    <button class="btn btn-ghost" on:click={() => choose({ id: 'manual', label: 'Manual', emoji: '📝' })}>
      Quick note
    </button>
    <button class="btn btn-primary" on:click={() => choose({ id: 'stressed', label: 'Stressed', emoji: '😰' })}>
      I need a quick reset
    </button>
  </div>
</section>
