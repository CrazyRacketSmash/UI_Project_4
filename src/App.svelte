<script>
  import { onMount } from 'svelte';
  import MoodPicker from './components/MoodPicker.svelte';
  import BreathingExercise from './components/BreathingExercise.svelte';

  let stage = 'picker'; // 'picker' | 'breath' | 'done'
  let mood = null;

  function onChoose(selected) {
    mood = selected;
    // Transition to breath if mood is one that benefits from grounding
    stage = 'breath';
  }

  function onFinished() {
    stage = 'done';
  }

  function backToPicker() {
    stage = 'picker';
    mood = null;
  }

  let suggestionText = '';
  $: if (mood) {
    if (mood.id === 'stressed') suggestionText = 'Try a short breathing exercise to calm your nervous system.';
    else if (mood.id === 'overwhelmed') suggestionText = 'Focus on the breath for 1 minute to create space.';
    else if (mood.id === 'lonely') suggestionText = 'A grounding exercise may help, and you might consider reaching out to someone you trust.';
    else suggestionText = 'A short pause and breathing can help reset.';
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
    {:else if stage === 'breath'}
      <div class="center">
        <p class="small" style="margin-bottom:12px">{suggestionText}</p>
        <BreathingExercise on:done={onFinished} />
        <div class="actions" style="justify-content:center; margin-top:12px;">
          <button class="btn btn-ghost" on:click={backToPicker}>Back</button>
        </div>
      </div>
    {:else}
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
