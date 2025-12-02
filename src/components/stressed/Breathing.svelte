<script>
  import { createEventDispatcher, onMount, onDestroy } from 'svelte';
  const dispatch = createEventDispatcher();

  // Settings (exposed as props so parent can control per-mood)
  export let cycles = 4; // total inhale/exhale cycles
  export let inhale = 4; // seconds
  export let hold = 0; // optional hold
  export let exhale = 6; // seconds
  export let autoStart = true; // whether to auto-start after mount

  // control circle transition timing based on step duration
  let circleTransition = '900ms cubic-bezier(.22,.9,.34,1)';

  let phase = 'ready'; // 'inhale'|'hold'|'exhale'|'done'
  let text = 'Get ready';
  let progress = 0;
  let circleScale = 1;
  let currentCycle = 0;
  let running = false;
  let timerId = null;

  function start() {
    if (running) return;
    running = true;
    currentCycle = 0;
    nextInhale();
  }

  function nextInhale() {
    phase = 'inhale';
    text = 'Breathe in';
    animateCircle(inhale, 1.28); // expand
    stepTimer(inhale, () => {
      if (hold > 0) {
        phase = 'hold';
        text = 'Hold';
        animateCircle(hold, 1.0);
        stepTimer(hold, nextExhale);
      } else {
        nextExhale();
      }
    });
  }

  function nextExhale() {
    phase = 'exhale';
    text = 'Breathe out';
    animateCircle(exhale, 0.72); // contract
    stepTimer(exhale, () => {
      currentCycle++;
      if (currentCycle >= cycles) finish();
      else nextInhale();
    });
  }

  function finish() {
    phase = 'done';
    text = 'Complete — well done';
    running = false;
    dispatch('done');
  }

  function stepTimer(seconds, cb) {
    // cancel existing
    if (timerId) clearInterval(timerId);
    let start = Date.now();
    const total = seconds * 1000;
    timerId = setInterval(() => {
      const elapsed = Date.now() - start;
      progress = Math.min(1, elapsed / total);
      if (elapsed >= total) {
        clearInterval(timerId);
        timerId = null;
        progress = 0;
        cb && cb();
      }
    }, 100);
  }

  function animateCircle(seconds, targetScale) {
    // animate scale over the duration using CSS transitions
    circleScale = targetScale;
    // update transition duration to match the step (minimum 400ms to avoid extremely short transitions)
    circleTransition = `${Math.max(400, Math.round(seconds * 1000))}ms cubic-bezier(.22,.9,.34,1)`;
  }

  onMount(() => {
    // auto-start small breathing after mount (only if parent allows)
    const t = setTimeout(() => {
      if (autoStart) start();
    }, 350);
    return () => clearTimeout(t);
  });

  onDestroy(() => {
    if (timerId) clearInterval(timerId);
  });
</script>

<div class="breath-wrapper" aria-live="polite">
  <div class="breath-circle" style="transform: scale({circleScale}); transition: transform {circleTransition}; background: radial-gradient(circle at 30% 30%, rgba(79,156,232,0.14), rgba(96,165,250,0.06));">
    <div style="text-align:center">
      <div style="font-size:2.8rem; line-height:1">{phase === 'inhale' ? '↗' : phase === 'exhale' ? '↘' : '●'}</div>
      <div class="breath-text">{text}</div>
    </div>
  </div>

  <div style="width:100%; display:flex; justify-content:center;">
    <div style="width:80%; max-width:420px;">
      <div class="small" style="text-align:center">{phase === 'done' ? 'You can take a moment and notice how you feel.' : 'Follow the circle — inhale when it expands, exhale when it contracts.'}</div>
    </div>
  </div>

  <div style="display:flex; gap:10px; margin-top:6px;">
    <button class="btn btn-ghost" on:click={() => { if (running) { running=false; if (timerId) clearInterval(timerId); phase='ready'; text='Paused' } else start(); }}>
      {running ? 'Pause' : 'Start'}
    </button>
    <button class="btn btn-primary" on:click={() => { if (timerId) clearInterval(timerId); finish(); }}>
      Finish
    </button>
  </div>
</div>

<style>
  /* component-scoped small tweaks */
  .breath-circle { transition: transform .9s cubic-bezier(.22,.9,.34,1); display:flex; align-items:center; justify-content:center; border-radius:50%; }
</style>
