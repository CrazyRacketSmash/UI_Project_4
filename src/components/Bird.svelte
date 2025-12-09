<script>
  import { createEventDispatcher, onMount } from 'svelte';
  const dispatch = createEventDispatcher();

  let audioEl;
  let isPlaying = false;
  let audioError = false;

  onMount(() => {
    // Auto play sound
    if (audioEl) {
      const playPromise = audioEl.play();
      if (playPromise !== undefined) {
        playPromise
          .then(() => {
            isPlaying = true;
          })
          .catch(() => {
            // Browser blocked autoplay or audio failed to load
            isPlaying = false;
          });
      }
    }
  });

  function toggleAudio() {
    if (!audioEl) return;
    if (audioEl.paused) {
      audioEl.play().then(() => {
        isPlaying = true;
      }).catch(() => {
        audioError = true;
      });
    } else {
      audioEl.pause();
      isPlaying = false;
    }
  }

  function onAudioError() {
    audioError = true;
    isPlaying = false;
  }

  function back() {
    if (audioEl) audioEl.pause();
    dispatch('back');
  }
</script>

<div class="bird-scene">
  <!-- Background landscape -->
  <div class="sky"></div>
  <div class="hills"></div>
  
  <!-- Flying birds -->
  <div class="bird bird-1" aria-hidden="true">
    <svg viewBox="0 0 24 24" width="32" height="32">
      <path d="M3 12c2-2 6-3 10-3s8 1 10 3c0 0-1 2-3 3-2 0-6 1-7 1s-5-1-7-1c-2-1-3-3-3-3z" fill="#fff" opacity="0.9"/>
    </svg>
  </div>

  <div class="bird bird-2" aria-hidden="true">
    <svg viewBox="0 0 24 24" width="28" height="28">
      <path d="M3 12c2-2 6-3 10-3s8 1 10 3c0 0-1 2-3 3-2 0-6 1-7 1s-5-1-7-1c-2-1-3-3-3-3z" fill="#fff" opacity="0.85"/>
    </svg>
  </div>

  <div class="bird bird-3" aria-hidden="true">
    <svg viewBox="0 0 24 24" width="30" height="30">
      <path d="M3 12c2-2 6-3 10-3s8 1 10 3c0 0-1 2-3 3-2 0-6 1-7 1s-5-1-7-1c-2-1-3-3-3-3z" fill="#fff" opacity="0.8"/>
    </svg>
  </div>

  <div class="bird bird-4" aria-hidden="true">
    <svg viewBox="0 0 24 24" width="26" height="26">
      <path d="M3 12c2-2 6-3 10-3s8 1 10 3c0 0-1 2-3 3-2 0-6 1-7 1s-5-1-7-1c-2-1-3-3-3-3z" fill="#fff" opacity="0.75"/>
    </svg>
  </div>

  <div class="bird bird-5" aria-hidden="true">
    <svg viewBox="0 0 24 24" width="29" height="29">
      <path d="M3 12c2-2 6-3 10-3s8 1 10 3c0 0-1 2-3 3-2 0-6 1-7 1s-5-1-7-1c-2-1-3-3-3-3z" fill="#fff" opacity="0.88"/>
    </svg>
  </div>

  <!-- Ambient audio -->
  <audio
    bind:this={audioEl}
    loop
    on:error={onAudioError}
    autoplay
    muted={false}
    playsinline
  >
    <source src="https://archive.org/download/Red_Library_Animals_Birds/R04-34-Bird%20Songs.mp3" type="audio/mpeg" />
    <!-- If audio gives errors -->
    Your browser doesn't support the <code>audio</code> element.
  </audio>

  <!-- Controls overlay -->
  <div class="controls">
    <button class="btn btn-audio" on:click={toggleAudio} title={isPlaying ? "Pause audio" : "Play audio"}>
      {audioError ? '❌' : (isPlaying ? '🔊' : '🔇')}
    </button>
    {#if audioError}
      <span class="small" style="color:#666;">Audio unavailable</span>
    {/if}
    <button class="btn btn-back" on:click={back}>Back</button>
  </div>

  <!-- Center message -->
  <div class="message">
    <h2>Take a peaceful moment</h2>
    <p>Breathe, observe the birds, and find calm.</p>
  </div>
</div>

<style>
  .bird-scene {
    position: fixed;
    inset: 0;
    overflow: hidden;
    display: flex;
    align-items: center;
    justify-content: center;
  }

  /* Sky gradient */
  .sky {
    position: absolute;
    inset: 0;
    background: linear-gradient(180deg, #87ceeb 0%, #e0f6ff 50%, #fff5e6 100%);
    z-index: 1;
  }

  /* Hills foreground */
  .hills {
    position: absolute;
    bottom: 0;
    left: 0;
    right: 0;
    height: 200px;
    background: linear-gradient(180deg, rgba(144,238,144,0.4), rgba(34,139,34,0.6));
    z-index: 2;
    clip-path: polygon(0 40%, 15% 35%, 30% 45%, 50% 30%, 70% 40%, 85% 32%, 100% 45%, 100% 100%, 0 100%);
  }

  /* Flying birds */
  .bird {
    position: absolute;
    z-index: 3;
    filter: drop-shadow(0 2px 4px rgba(0,0,0,0.1));
  }

  .bird-1 {
    top: 15%;
    animation: fly-1 12s linear infinite;
  }

  .bird-2 {
    top: 25%;
    animation: fly-2 14s linear infinite;
  }

  .bird-3 {
    top: 35%;
    animation: fly-3 16s linear infinite;
  }

  .bird-4 {
    top: 20%;
    animation: fly-4 13s linear infinite;
  }

  .bird-5 {
    top: 30%;
    animation: fly-5 15s linear infinite;
  }

  @keyframes fly-1 {
    0% { left: -50px; }
    100% { left: 100vw; }
  }

  @keyframes fly-2 {
    0% { right: -50px; }
    100% { right: 100vw; }
  }

  @keyframes fly-3 {
    0% { left: -50px; }
    100% { left: 100vw; }
  }

  @keyframes fly-4 {
    0% { right: -50px; }
    100% { right: 100vw; }
  }

  @keyframes fly-5 {
    0% { left: -50px; }
    100% { left: 100vw; }
  }

  /* Center message */
  .message {
    position: relative;
    z-index: 4;
    text-align: center;
    color: Black;
    text-shadow: 0 2px 8px rgba(0,0,0,0.2);
    pointer-events: none;
  }

  .message h2 {
    font-size: 2rem;
    margin: 0 0 12px 0;
    font-weight: 700;
  }

  .message p {
    font-size: 1.1rem;
    margin: 0;
    opacity: 0.9;
  }

  /* Controls */
  .controls {
    position: absolute;
    bottom: 24px;
    left: 50%;
    transform: translateX(-50%);
    z-index: 10;
    display: flex;
    gap: 12px;
  }

  .btn {
    padding: 10px 16px;
    border-radius: 8px;
    border: none;
    cursor: pointer;
    font-weight: 600;
    transition: transform 0.12s ease, box-shadow 0.12s ease;
  }

  .btn-audio {
    background: rgba(255, 255, 255, 0.9);
    color: #064e6b;
    font-size: 1.2rem;
    width: 48px;
    height: 48px;
    display: flex;
    align-items: center;
    justify-content: center;
    padding: 0;
  }

  .btn-audio:hover {
    transform: translateY(-2px);
    box-shadow: 0 8px 16px rgba(0, 0, 0, 0.15);
  }

  .btn-back {
    background: linear-gradient(90deg, #6aa6ff, #7ee3ff);
    color: white;
  }

  .btn-back:hover {
    transform: translateY(-2px);
    box-shadow: 0 10px 24px rgba(79, 156, 232, 0.2);
  }

  .small { font-size: 0.9rem; }
</style>
