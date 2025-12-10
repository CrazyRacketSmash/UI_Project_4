<script>
  import { createEventDispatcher, onMount } from 'svelte';
  import  music  from '../music/Relaxing-ambient-music.mp3';
  const dispatch = createEventDispatcher();

  let audioElMusic;
  let audioElBirds;
  let isPlayingMusic = false;
  let isPlayingBirds = false;
  let audioError = false;

  let showInitialMessage = true;
  let currentQuoteIndex = 0;
  let fadingOut = false;

  const encouragingQuotes = [
    { text: "You are stronger than you know", sub: "Every breath is a step forward" },
    { text: "It's okay to not be okay", sub: "Healing is not linear" },
    { text: "You deserve peace and happiness", sub: "Be gentle with yourself today" },
    { text: "This moment will pass", sub: "You've overcome challenges before" },
    { text: "Your feelings are valid", sub: "Take it one day at a time" },
    { text: "You are not alone in this", sub: "Reach out when you need support" },
    { text: "Small steps are still progress", sub: "Celebrate every victory, no matter how small" },
    { text: "You are worthy of love and care", sub: "Including love and care from yourself" },
    { text: "Rest is not a weakness", sub: "Taking breaks helps you recharge" },
    { text: "Your journey is unique", sub: "Don't compare your chapter 1 to someone's chapter 20" },
    { text: "It's brave to ask for help", sub: "Strength is knowing when to reach out" },
    { text: "You've survived 100% of your hardest days", sub: "That's a pretty good track record" },
    { text: "Progress isn't always visible", sub: "Inner growth counts too" },
    { text: "You don't have to be perfect", sub: "You just have to be you" },
    { text: "Your mental health matters", sub: "Prioritizing it is not selfish" },
    { text: "Bad days don't last forever", sub: "Tomorrow is a new opportunity" },
    { text: "You are doing the best you can", sub: "And that is enough" },
    { text: "It's okay to take things slowly", sub: "There's no rush in healing" },
    { text: "You deserve kindness", sub: "Especially from yourself" },
    { text: "Every ending is a new beginning", sub: "Trust the process of change" }
  ];

  onMount(() => {
    // Auto play both sounds
    playBothAudios();

    // Hide initial message after 8 seconds
    setTimeout(() => {
      fadingOut = true;
      setTimeout(() => {
        showInitialMessage = false;
        fadingOut = false;
      }, 1500); // Wait for fade-out animation to complete
    }, 8000);

    // Rotate quotes every 12 seconds after initial message fades
    setTimeout(() => {
      const quoteInterval = setInterval(() => {
        if (!showInitialMessage) {
          fadingOut = true;
          setTimeout(() => {
            currentQuoteIndex = (currentQuoteIndex + 1) % encouragingQuotes.length;
            fadingOut = false;
          }, 1500); // Wait for fade-out animation to complete
        }
      }, 12000);

      return () => clearInterval(quoteInterval);
    }, 9500); // 8000 initial delay + 1500 fade-out duration
  });

  function playBothAudios() {
    const playMusic = audioElMusic?.play();
    const playBirds = audioElBirds?.play();

    if (playMusic !== undefined) {
      playMusic
        .then(() => {
          isPlayingMusic = true;
        })
        .catch(() => {
          isPlayingMusic = false;
        });
    }

    if (playBirds !== undefined) {
      playBirds
        .then(() => {
          isPlayingBirds = true;
        })
        .catch(() => {
          isPlayingBirds = false;
        });
    }
  }

  function toggleAudio() {
    if (!audioElMusic || !audioElBirds) return;

    // Toggle both together
    if (isPlayingMusic || isPlayingBirds) {
      audioElMusic.pause();
      audioElBirds.pause();
      isPlayingMusic = false;
      isPlayingBirds = false;
    } else {
      playBothAudios();
    }
  }

  function toggleMusic() {
    if (!audioElMusic) return;
    if (audioElMusic.paused) {
      audioElMusic.play().then(() => {
        isPlayingMusic = true;
      }).catch(() => {
        audioError = true;
      });
    } else {
      audioElMusic.pause();
      isPlayingMusic = false;
    }
  }

  function toggleBirds() {
    if (!audioElBirds) return;
    if (audioElBirds.paused) {
      audioElBirds.play().then(() => {
        isPlayingBirds = true;
      }).catch(() => {
        audioError = true;
      });
    } else {
      audioElBirds.pause();
      isPlayingBirds = false;
    }
  }

  function onAudioError() {
    audioError = true;
    isPlayingMusic = false;
    isPlayingBirds = false;
  }

  function back() {
    if (audioElMusic) audioElMusic.pause();
    if (audioElBirds) audioElBirds.pause();
    dispatch('back');
  }
</script>

<div class="bird-scene">
  <!-- Background landscape -->
  <div class="sky"></div>
  
  <!-- Clouds -->
  <div class="cloud cloud-1" aria-hidden="true"></div>
  <div class="cloud cloud-2" aria-hidden="true"></div>
  <div class="cloud cloud-3" aria-hidden="true"></div>
  <div class="cloud cloud-4" aria-hidden="true"></div>
  <div class="cloud cloud-5" aria-hidden="true"></div>

  <!-- Hills foreground -->
  <div class="hills"></div>

  <!-- Creek flowing across hills -->
  <div class="creek" aria-hidden="true">
    <svg width="100%" height="400" viewBox="0 0 1200 400" preserveAspectRatio="none" style="position:absolute; bottom:0; left:0; z-index:2;">
      <!-- Main creek path -->
      <path 
        d="M 0 270 Q 150 250 300 270 T 600 270 T 900 270 T 1200 270" 
        stroke="url(#creek-gradient)" 
        stroke-width="28" 
        fill="none" 
        stroke-linecap="round"
        opacity="0.75"
      />
      <!-- Creek edges for depth -->
      <path 
        d="M 0 270 Q 150 250 300 270 T 600 270 T 900 270 T 1200 270" 
        stroke="rgba(140, 180, 200, 0.4)" 
        stroke-width="32" 
        fill="none" 
        stroke-linecap="round"
        opacity="0.5"
      />
      <!-- Creek highlights -->
      <path 
        d="M 0 265 Q 150 245 300 265 T 600 265 T 900 265 T 1200 265" 
        stroke="rgba(255,255,255,0.4)" 
        stroke-width="8" 
        fill="none" 
        stroke-linecap="round"
        opacity="0.6"
        class="creek-shimmer"
      />
      <!-- Flowing ripples -->
      <path 
        d="M 0 275 Q 150 255 300 275 T 600 275 T 900 275 T 1200 275" 
        stroke="rgba(255,255,255,0.25)" 
        stroke-width="3" 
        fill="none" 
        stroke-linecap="round"
        class="creek-ripple creek-ripple-1"
      />
      <path 
        d="M 0 268 Q 150 248 300 268 T 600 268 T 900 268 T 1200 268" 
        stroke="rgba(255,255,255,0.2)" 
        stroke-width="2" 
        fill="none" 
        stroke-linecap="round"
        class="creek-ripple creek-ripple-2"
      />
      <!-- Flowing dots -->
      <circle cx="200" cy="270" r="2" fill="rgba(255,255,255,0.6)" class="flow-dot flow-dot-1" />
      <circle cx="500" cy="268" r="2.5" fill="rgba(255,255,255,0.5)" class="flow-dot flow-dot-2" />
      <circle cx="800" cy="272" r="2" fill="rgba(255,255,255,0.55)" class="flow-dot flow-dot-3" />
      <defs>
        <linearGradient id="creek-gradient" x1="0%" y1="0%" x2="100%" y2="0%">
          <stop offset="0%" style="stop-color:#88C5D4;stop-opacity:1" />
          <stop offset="25%" style="stop-color:#A8DADC;stop-opacity:1" />
          <stop offset="50%" style="stop-color:#C5E8EA;stop-opacity:1" />
          <stop offset="75%" style="stop-color:#A8DADC;stop-opacity:1" />
          <stop offset="100%" style="stop-color:#88C5D4;stop-opacity:1" />
        </linearGradient>
      </defs>
    </svg>
  </div>

  <!-- Big Background Trees -->
  <div class="tree tree-bg-1" aria-hidden="true">
    <svg width="90" height="120" viewBox="0 0 24 32">
      <circle cx="12" cy="10" r="9" fill="#B6EBC0"/>
      <rect x="11" y="17" width="3" height="12" rx="1" fill="#A67C52"/>
    </svg>
  </div>

  <div class="tree tree-bg-2" aria-hidden="true">
    <svg width="90" height="120" viewBox="0 0 24 32">
      <circle cx="12" cy="11" r="8" fill="#C7F2CC"/>
      <rect x="11" y="18" width="3" height="11" rx="1" fill="#A67C52"/>
    </svg>
  </div>

  <div class="tree tree-bg-3" aria-hidden="true">
    <svg width="90" height="120" viewBox="0 0 24 32">
      <circle cx="12" cy="10" r="9" fill="#B4E8B6"/>
      <rect x="11" y="17" width="3" height="12" rx="1" fill="#9F754B"/>
    </svg>
  </div>

  <!-- Left Trees -->
  <div class="tree tree-left-1" aria-hidden="true">
    <svg width="65" height="90" viewBox="0 0 24 32">
      <circle cx="12" cy="11" r="7" fill="#C7F5D4"/>
      <rect x="11" y="18" width="3" height="10" rx="1" fill="#A67C52"/>
    </svg>
  </div>

  <div class="tree tree-left-2" aria-hidden="true">
    <svg width="60" height="80" viewBox="0 0 24 32">
      <circle cx="12" cy="12" r="6" fill="#D3F8DD"/>
      <rect x="11" y="18" width="3" height="9" rx="1" fill="#A67C52"/>
    </svg>
  </div>

  <div class="tree tree-left-3" aria-hidden="true">
    <svg width="55" height="70" viewBox="0 0 24 32">
      <circle cx="12" cy="12" r="5.5" fill="#C6F1CB"/>
      <rect x="11" y="18" width="3" height="8" rx="1" fill="#A67C52"/>
    </svg>
  </div>

  <!-- Right Trees -->
  <div class="tree tree-right-1" aria-hidden="true">
    <svg width="65" height="90" viewBox="0 0 24 32">
      <circle cx="12" cy="11" r="7" fill="#C7F5D4"/>
      <rect x="11" y="18" width="3" height="10" rx="1" fill="#A67C52"/>
    </svg>
  </div>

  <div class="tree tree-right-2" aria-hidden="true">
    <svg width="60" height="80" viewBox="0 0 24 32">
      <circle cx="12" cy="12" r="6" fill="#D3F8DD"/>
      <rect x="11" y="18" width="3" height="9" rx="1" fill="#A67C52"/>
    </svg>
  </div>

  <div class="tree tree-right-3" aria-hidden="true">
    <svg width="55" height="70" viewBox="0 0 24 32">
      <circle cx="12" cy="12" r="5.5" fill="#C6F1CB"/>
      <rect x="11" y="18" width="3" height="8" rx="1" fill="#A67C52"/>
    </svg>
  </div>

  <!-- Small Trees -->
  <div class="tree tree-small-1" aria-hidden="true">
    <svg width="45" height="55" viewBox="0 0 24 32">
      <circle cx="12" cy="13" r="4.5" fill="#DDFCE6"/>
      <rect x="11" y="17" width="2.5" height="7" rx="1" fill="#9F754B"/>
    </svg>
  </div>

  <div class="tree tree-small-2" aria-hidden="true">
    <svg width="40" height="50" viewBox="0 0 24 32">
      <circle cx="12" cy="13" r="4" fill="#E2FFE9"/>
      <rect x="11" y="17" width="2.5" height="7" rx="1" fill="#9F754B"/>
    </svg>
  </div>

  <div class="tree tree-small-3" aria-hidden="true">
    <svg width="40" height="50" viewBox="0 0 24 32">
      <circle cx="12" cy="13" r="4" fill="#D7F9E0"/>
      <rect x="11" y="17" width="2.5" height="7" rx="1" fill="#9F754B"/>
    </svg>
  </div>
  
  <!-- Rising bubbles -->
  <div class="bubbles-container" aria-hidden="true">
    <div class="bubble bubble-1"></div>
    <div class="bubble bubble-2"></div>
    <div class="bubble bubble-3"></div>
    <div class="bubble bubble-4"></div>
    <div class="bubble bubble-5"></div>
    <div class="bubble bubble-6"></div>
    <div class="bubble bubble-7"></div>
  </div>

  <!-- Flying birds -->
  <div class="bird bird-1" aria-hidden="true">
    <svg viewBox="0 0 48 48" width="48" height="48">
      <g>
        <!-- Body -->
        <ellipse cx="24" cy="24" rx="8" ry="6" fill="#FFD700"/>
        <!-- Head -->
        <circle cx="20" cy="18" r="4" fill="#FFD700"/>
        <!-- Eye -->
        <circle cx="18" cy="16" r="1.5" fill="#333"/>
        <!-- Beak -->
        <path d="M16 18 L12 18" stroke="#FF8C00" stroke-width="2" stroke-linecap="round"/>
        <!-- Wing -->
        <path d="M28 22 Q36 18 40 12" stroke="#FFA500" stroke-width="3" stroke-linecap="round" fill="none"/>
        <!-- Tail -->
        <path d="M16 26 Q12 28 10 32" stroke="#FFD700" stroke-width="2.5" stroke-linecap="round" fill="none"/>
      </g>
    </svg>
  </div>

  <div class="bird bird-2" aria-hidden="true">
    <svg viewBox="0 0 48 48" width="44" height="44">
      <g>
        <ellipse cx="24" cy="24" rx="7.5" ry="5.5" fill="#FFED4E"/>
        <circle cx="21" cy="19" r="3.5" fill="#FFED4E"/>
        <circle cx="19" cy="17" r="1.2" fill="#333"/>
        <path d="M17 19 L13 19" stroke="#FF9500" stroke-width="1.8" stroke-linecap="round"/>
        <path d="M27 21 Q34 17 38 11" stroke="#FFB700" stroke-width="2.5" stroke-linecap="round" fill="none"/>
        <path d="M16 25 Q13 27 11 30" stroke="#FFED4E" stroke-width="2" stroke-linecap="round" fill="none"/>
      </g>
    </svg>
  </div>

  <div class="bird bird-3" aria-hidden="true">
    <svg viewBox="0 0 48 48" width="46" height="46">
      <g>
        <ellipse cx="24" cy="24" rx="8" ry="6" fill="#FFC700"/>
        <circle cx="20" cy="18" r="4" fill="#FFC700"/>
        <circle cx="18" cy="16" r="1.5" fill="#333"/>
        <path d="M16 18 L12 18" stroke="#FF9000" stroke-width="2" stroke-linecap="round"/>
        <path d="M28 22 Q36 18 40 12" stroke="#FFB300" stroke-width="3" stroke-linecap="round" fill="none"/>
        <path d="M16 26 Q12 28 10 32" stroke="#FFC700" stroke-width="2.5" stroke-linecap="round" fill="none"/>
      </g>
    </svg>
  </div>

  <div class="bird bird-4" aria-hidden="true">
    <svg viewBox="0 0 48 48" width="42" height="42">
      <g>
        <ellipse cx="24" cy="24" rx="7" ry="5" fill="#FFD700"/>
        <circle cx="21" cy="19" r="3.2" fill="#FFD700"/>
        <circle cx="19" cy="17" r="1" fill="#333"/>
        <path d="M17 19 L14 19" stroke="#FF8A00" stroke-width="1.6" stroke-linecap="round"/>
        <path d="M27 21 Q33 17 36 12" stroke="#FFA500" stroke-width="2.5" stroke-linecap="round" fill="none"/>
        <path d="M16 25 Q13 26 11 29" stroke="#FFD700" stroke-width="2" stroke-linecap="round" fill="none"/>
      </g>
    </svg>
  </div>

  <div class="bird bird-5" aria-hidden="true">
    <svg viewBox="0 0 48 48" width="45" height="45">
      <g>
        <ellipse cx="24" cy="24" rx="8" ry="6" fill="#FFE44D"/>
        <circle cx="20" cy="18" r="4" fill="#FFE44D"/>
        <circle cx="18" cy="16" r="1.5" fill="#333"/>
        <path d="M16 18 L12 18" stroke="#FF9A00" stroke-width="2" stroke-linecap="round"/>
        <path d="M28 22 Q35 18 39 12" stroke="#FFC300" stroke-width="3" stroke-linecap="round" fill="none"/>
        <path d="M16 26 Q12 28 10 32" stroke="#FFE44D" stroke-width="2.5" stroke-linecap="round" fill="none"/>
      </g>
    </svg>
  </div>

  <!-- Music -->
  <audio
    bind:this={audioElMusic}
    loop
    on:error={onAudioError}
    autoplay
    muted={false}
    playsinline
  >
    <source src={music} type="audio/mpeg" />
    Your browser doesn't support the audio element.
  </audio>

  <!-- Birds Chirping -->
  <audio
    bind:this={audioElBirds}
    loop
    on:error={onAudioError}
    autoplay
    muted={false}
    playsinline
  >
    <source src="https://www.freesoundslibrary.com/wp-content/uploads/2022/01/forest-birds-sound-effect-relaxing-nature-ambience.mp3" type="audio/mpeg" />
    <source src="https://orangefreesounds.com/wp-content/uploads/2025/06/Morning-birds-chirping-sound-effect.mp3" type="audio/mpeg" />
    <source src="https://orangefreesounds.com/wp-content/uploads/2016/05/Chirping-birds.mp3" type="audio/mpeg" />
    Your browser doesn't support the audio element.
  </audio>

  <!-- Controls overlay -->
  <div class="controls">
    <!-- Master toggle both -->
    <button class="btn btn-audio" on:click={toggleAudio} title={(isPlayingMusic || isPlayingBirds) ? "Pause all" : "Play all"}>
      {audioError ? '❌' : (isPlayingMusic || isPlayingBirds ? '🔊' : '🔇')}
    </button>

    <!-- Music toggle -->
    <button class="btn btn-audio btn-secondary" on:click={toggleMusic} title={isPlayingMusic ? "Pause music" : "Play music"}>
      {isPlayingMusic ? '🎵' : '🔇'}
    </button>

    <!-- Birds toggle -->
    <button class="btn btn-audio btn-secondary" on:click={toggleBirds} title={isPlayingBirds ? "Pause birds" : "Play birds"}>
      {isPlayingBirds ? '🐦' : '🔇'}
    </button>

    {#if audioError}
      <span class="small" style="color:#666;">Audio unavailable</span>
    {/if}
    <button class="btn btn-back" on:click={back}>Back</button>
  </div>

  <!-- Center message -->
  <div class="message">
    {#if showInitialMessage}
      <div class="initial-message" class:fade-out={fadingOut}>
        <h2>Take a peaceful moment</h2>
        <p>Breathe, observe the birds, and find calm.</p>
      </div>
    {:else}
      <div class="quote-message" class:fade-out={fadingOut} key={currentQuoteIndex}>
        <h2>{encouragingQuotes[currentQuoteIndex].text}</h2>
        <p>{encouragingQuotes[currentQuoteIndex].sub}</p>
      </div>
    {/if}
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

  /* Sky gradient*/
  .sky {
    position: absolute;
    inset: 0;
    background: linear-gradient(180deg, #9f94b4 0%, #ece0cf 25%, #ffe2b8 40%, #f7d8aa 55%, #c2f1c4 75%, #71eb76 100%);
    z-index: 1;
  }

  /* Clouds */
  .cloud {
    position: absolute;
    z-index: 2;
    animation: float 20s ease-in-out infinite;
  }

  .cloud-1 {
    top: 60px;
    left: 60px;
    width: 120px;
    height: 40px;
    background: radial-gradient(ellipse 60px 20px at 20%, #FFF5E6), 
                radial-gradient(ellipse 50px 25px at 60%, #FFEAD5);
    border-radius: 50px;
    opacity: 0.85;
    box-shadow: 0 0 15px rgba(255, 180, 100, 0.2);
  }

  .cloud-2 {
    top: 200px;
    right: 80px;
    width: 100px;
    height: 35px;
    background: radial-gradient(ellipse 50px 18px at 20%, #FFF0E0), 
                radial-gradient(ellipse 45px 22px at 55%, #FFE5CF);
    border-radius: 50px;
    opacity: 0.8;
    box-shadow: 0 0 15px rgba(255, 180, 100, 0.15);
    animation-delay: 10s;
  }

  .cloud-3 {
    top: 120px;
    left: 30%;
    width: 140px;
    height: 45px;
    background: radial-gradient(ellipse 70px 22px at 20%, #FFFBF0), 
                radial-gradient(ellipse 60px 28px at 65%, #FFEDE0);
    border-radius: 50px;
    opacity: 0.75;
    box-shadow: 0 0 15px rgba(255, 180, 100, 0.18);
    animation-delay: 5s;
  }

  .cloud-4 {
    top: 170px;
    right: 25%;
    width: 110px;
    height: 38px;
    background: radial-gradient(ellipse 55px 19px at 20%, #FFF8EC), 
                radial-gradient(ellipse 48px 24px at 60%, #FFE8D8);
    border-radius: 50px;
    opacity: 0.82;
    box-shadow: 0 0 15px rgba(255, 180, 100, 0.16);
    animation-delay: 15s;
  }

  .cloud-5 {
    top: 80px;
    right: 15%;
    width: 95px;
    height: 32px;
    background: radial-gradient(ellipse 48px 16px at 20%, #FFF5E6), 
                radial-gradient(ellipse 42px 20px at 58%, #FFEAD5);
    border-radius: 50px;
    opacity: 0.78;
    box-shadow: 0 0 14px rgba(255, 180, 100, 0.14);
    animation-delay: 8s;
  }

  @keyframes float {
    0% { transform: translateX(0); }
    50% { transform: translateX(30px); }
    100% { transform: translateX(0); }
  }

  /* Trees */
  .tree {
    position: absolute;
    z-index: 2;
  }

  /* Background large trees */
  .tree-bg-1 {
    bottom: 150px;
    left: 5%;
    width: 40px;
    height: 100px;
    opacity: 0.6;
  }

  .tree-bg-2 {
    bottom: 160px;
    left: 50%;
    width: 45px;
    height: 110px;
    opacity: 0.55;
  }

  .tree-bg-3 {
    bottom: 150px;
    right: 5%;
    width: 42px;
    height: 105px;
    opacity: 0.58;
  }

  /* Medium trees on left */
  .tree-left-1 {
    bottom: 60px;
    left: 20px;
    width: 70px;
    height: 220px;
  }

  .tree-left-2 {
    bottom: 80px;
    left: 120px;
    width: 65px;
    height: 200px;
  }

  .tree-left-3 {
    bottom: 60px;
    left: 200px;
    width: 55px;
    height: 180px;
  }

  /* Medium trees on right */
  .tree-right-1 {
    bottom: 70px;
    right: 40px;
    width: 70px;
    height: 220px;
  }

  .tree-right-2 {
    bottom: 90px;
    right: 130px;
    width: 60px;
    height: 190px;
  }

  .tree-right-3 {
    bottom: 60px;
    right: 210px;
    width: 55px;
    height: 180px;
  }
  
  /* Small trees scattered in foreground */
  .tree-small-1 {
    bottom: 80px;
    left: 15%;
    width: 30px;
    height: 80px;
  }

  .tree-small-2 {
    bottom: 90px;
    left: 70%;
    width: 28px;
    height: 75px;
  }

  .tree-small-3 {
    bottom: 70px;
    right: 20%;
    width: 32px;
    height: 85px;
  }

  .tree::before {
    content: '';
    position: absolute;
    bottom: 0;
    left: 50%;
    transform: translateX(-50%);
    width: 20%;
    height: 25%;
    background: linear-gradient(90deg, #6B5437, #8B6F47);
    border-radius: 3px;
    box-shadow: inset -1px 0 2px rgba(0, 0, 0, 0.3);
  }

  .tree::after {
    content: '';
    position: absolute;
    top: 0;
    left: 50%;
    transform: translateX(-50%);
    width: 0;
    height: 0;
    border-left: 45% solid transparent;
    border-right: 45% solid transparent;
    border-bottom: 85% solid #1a7a1a;
    filter: drop-shadow(0 4px 8px rgba(0, 0, 0, 0.2));
  }

  /* Add second layer of foliage for depth */
  .tree-left-1::before, .tree-left-2::before, .tree-left-3::before,
  .tree-right-1::before, .tree-right-2::before, .tree-right-3::before,
  .tree-small-1::before, .tree-small-2::before, .tree-small-3::before,
  .tree-bg-1::before, .tree-bg-2::before, .tree-bg-3::before {
    box-shadow: inset -1px 0 3px rgba(0, 0, 0, 0.4), 0 2px 4px rgba(0, 0, 0, 0.2);
  }

  /* Hills foreground*/
  .hills {
    position: absolute;
    bottom: 0;
    left: 0;
    right: 0;
    height: 400px;
    background: linear-gradient(180deg, rgba(255, 170, 120, 0.25), rgba(180, 140, 80, 0.65));
    z-index: 1;
    clip-path: polygon(0 50%, 10% 45%, 20% 50%, 35% 40%, 50% 50%, 65% 38%, 80% 48%, 90% 42%, 100% 50%, 100% 100%, 0 100%);
  }

  /* Creek */
  .creek {
    position: absolute;
    bottom: 0;
    left: 0;
    right: 0;
    height: 400px;
    z-index: 2;
    pointer-events: none;
  }
  
  .creek-shimmer {
    animation: shimmer 2.5s ease-in-out infinite;
  }
  
  @keyframes shimmer {
    0%, 100% { opacity: 0.5; }
    50% { opacity: 0.8; }
  }

  .creek-ripple {
    animation: ripple 4s ease-in-out infinite;
  }

  .creek-ripple-1 {
    animation-delay: 0s;
  }

  .creek-ripple-2 {
    animation-delay: 2s;
  }

  @keyframes ripple {
    0%, 100% { 
      opacity: 0.2; 
      stroke-dasharray: 0, 100;
    }
    50% { 
      opacity: 0.5; 
      stroke-dasharray: 20, 80;
    }
  }

  .flow-dot {
    animation: flow 15s linear infinite;
  }

  .flow-dot-1 {
    animation-delay: 0s;
  }

  .flow-dot-2 {
    animation-delay: 4s;
  }

  .flow-dot-3 {
    animation-delay: 7s;
  }

  @keyframes flow {
    0% { 
      cx: 0; 
      opacity: 0; 
    }
    10% {
      opacity: 0.6;
    }
    90% {
      opacity: 0.4;
    }
    100% { 
      cx: 1200; 
      opacity: 0; 
    }
  }

  /* Rising Bubbles */
  .bubbles-container {
    position: absolute;
    bottom: 250px;
    left: 0;
    right: 0;
    height: 200px;
    z-index: 2;
    pointer-events: none;
  }

  .bubble {
    position: absolute;
    bottom: -40px;
    border-radius: 50%;
    opacity: 0;
    background: radial-gradient(circle at 35% 35%, rgba(255, 255, 255, 0.8), rgba(173, 216, 230, 0.4), rgba(100, 150, 200, 0.2));
    box-shadow: inset -2px -2px 5px rgba(0, 0, 0, 0.1), 0 4px 10px rgba(100, 150, 200, 0.2);
  }

  .bubble-1 {
    left: 10%;
    width: 20px;
    height: 20px;
    animation: rise 4s ease-in infinite;
  }

  .bubble-2 {
    left: 25%;
    width: 28px;
    height: 28px;
    animation: rise 4.5s ease-in 0.3s infinite;
  }

  .bubble-3 {
    left: 40%;
    width: 18px;
    height: 18px;
    animation: rise 4.2s ease-in 0.6s infinite;
  }

  .bubble-4 {
    left: 55%;
    width: 24px;
    height: 24px;
    animation: rise 4.8s ease-in 0.2s infinite;
  }

  .bubble-5 {
    left: 70%;
    width: 22px;
    height: 22px;
    animation: rise 4.3s ease-in 0.5s infinite;
  }

  .bubble-6 {
    left: 80%;
    width: 26px;
    height: 26px;
    animation: rise 4.6s ease-in 0.4s infinite;
  }

  .bubble-7 {
    left: 35%;
    width: 16px;
    height: 16px;
    animation: rise 3.9s ease-in 0.8s infinite;
  }

  @keyframes rise {
    0% {
      transform: translateY(0) translateX(0) scale(0.8);
      opacity: 0;
    }
    10% {
      opacity: 0.6;
    }
    90% {
      opacity: 0.4;
    }
    100% {
      transform: translateY(-250px) translateX(30px) scale(1);
      opacity: 0;
    }
  }

  /* Flying birds */
  .bird {
    position: absolute;
    z-index: 4;
    filter: drop-shadow(0 3px 6px rgba(0, 0, 0, 0.15));
  }

  .bird-1 {
    top: 15%;
    animation: fly-1 27s linear infinite;
  }

  .bird-2 {
    top: 25%;
    animation: fly-2 30s linear infinite;
  }

  .bird-3 {
    top: 35%;
    animation: fly-3 32s linear infinite;
  }

  .bird-4 {
    top: 20%;
    animation: fly-4 25s linear infinite;
  }

  .bird-5 {
    top: 30%;
    animation: fly-5 22s linear infinite;
  }

  @keyframes fly-1 {
    0% { left: -60px; }
    100% { left: 100vw; }
  }

  @keyframes fly-2 {
    0% { right: -60px; }
    100% { right: 100vw; }
  }

  @keyframes fly-3 {
    0% { left: -60px; }
    100% { left: 100vw; }
  }

  @keyframes fly-4 {
    0% { right: -60px; }
    100% { right: 100vw; }
  }

  @keyframes fly-5 {
    0% { left: -60px; }
    100% { left: 100vw; }
  }

  /* Center message */
  .message {
    position: relative;
    z-index: 5;
    text-align: center;
    color: #333;
    text-shadow: 0 2px 6px rgba(255, 255, 255, 0.6);
    pointer-events: none;
  }

  .initial-message {
    animation: fadeIn 1s ease-in;
  }

  .initial-message.fade-out,
  .quote-message.fade-out {
    animation: fadeOut 1.5s ease-out forwards;
  }

  .quote-message {
    animation: fadeIn 1.5s ease-in;
  }

  @keyframes fadeIn {
    from { opacity: 0; transform: translateY(10px); }
    to { opacity: 1; transform: translateY(0); }
  }

  @keyframes fadeOut {
    from { opacity: 1; transform: translateY(0); }
    to { opacity: 0; transform: translateY(-10px); }
  }

  .message h2 {
    font-size: 2.2rem;
    margin: 0 0 12px 0;
    font-weight: 700;
    color: #8B6F47;
  }

  .message p {
    font-size: 1.15rem;
    margin: 0;
    opacity: 0.85;
    color: #A0826D;
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
    background: rgba(255, 255, 255, 0.95);
    color: #064e6b;
    font-size: 1.2rem;
    width: 48px;
    height: 48px;
    display: flex;
    align-items: center;
    justify-content: center;
    padding: 0;
    box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
  }

  .btn-audio:hover {
    transform: translateY(-2px);
    box-shadow: 0 8px 16px rgba(0, 0, 0, 0.15);
  }

  .btn-secondary {
    width: 42px;
    height: 42px;
    font-size: 1rem;
    background: rgba(255, 255, 255, 0.85);
  }

  .btn-secondary:hover {
    transform: translateY(-2px);
    box-shadow: 0 6px 12px rgba(0, 0, 0, 0.12);
  }

  .btn-back {
    background: linear-gradient(90deg, #6aa6ff, #7ee3ff);
    color: white;
    box-shadow: 0 4px 12px rgba(106, 166, 255, 0.3);
  }

  .btn-back:hover {
    transform: translateY(-2px);
    box-shadow: 0 10px 24px rgba(79, 156, 232, 0.3);
  }

  .small { font-size: 0.9rem; }
</style>
