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

  // Rabbit hopping state
  let rabbitHoverCount = 0;
  let rabbitPosition = { x: 0, y: 0 }; // absolute position
  let isHopping = false;

  function handleRabbitHover() {
    if (rabbitHoverCount < 5) {
      isHopping = true;
      rabbitHoverCount++;
      
      // Move rabbit to random position on screen
      const viewportWidth = window.innerWidth;
      const viewportHeight = window.innerHeight;
      const newX = Math.random() * (viewportWidth - 150) + 75; // keep within bounds
      const newY = Math.random() * (viewportHeight - 150) + 75;
      
      rabbitPosition = { x: newX, y: newY };
      
      // Reset hopping animation after it completes
      setTimeout(() => {
        isHopping = false;
      }, 500);
    }
  }

  function handleAnimalClick(id) {
    if (id === 'rabbit' && rabbitHoverCount < 5) {
      // Block click until rabbit has hopped
      return;
    }
    choose({ id, label: id.charAt(0).toUpperCase() + id.slice(1), emoji: id === 'whale' ? '🐋' : id === 'rabbit' ? '🐰' : '🐦' });
  }

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
  <h3 class="h-title" style="font-size:1.1rem">How are you feeling right now? - Tap the mood that fits best</h3>
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
  <h2 class="h-title" style="font-size:1.1rem">Special Helpers</h2>
  <p class="small">Try one of the cute animals - <b><i><u>HEADS UP:</u></i></b> The rabbit seems to hop away when you try to click it!</p>

  <div class="image-actions" role="group" aria-label="Cute helpers">
      <button class="image-btn" on:click={() => handleAnimalClick('whale')} aria-label="Whale">
        <!-- Whale SVG -->
        <svg width="48" height="48" viewBox="0 0 24 24" fill="none" aria-hidden="false" role="img" xmlns="http://www.w3.org/2000/svg">
          <title>Whale</title>
          <desc>Stylized blue whale with small fin and spout</desc>
          <defs>
            <linearGradient id="gWhale" x1="0" x2="1" y1="0" y2="1">
              <stop offset="0%" stop-color="#7FC0FF"/>
              <stop offset="100%" stop-color="#2E83FF"/>
            </linearGradient>
          </defs>

          <!-- body -->
          <path d="M2 12c2-2 6-3 10-3s8 1 10 3c0 0-1 4-6 6-3 1-6 1-10 0C3 18 2 14 2 12z"
                fill="url(#gWhale)"/>
          <!-- fin -->
          <path d="M13 15c-.6.5-1.4.7-2 .6-.6-.1-1.2-.7-1.4-1.3.8-.2 1.9-.2 3 .7z" fill="#2A6FE6" opacity="0.95"/>
          <!-- eye -->
          <circle cx="7" cy="11" r="1.1" fill="#fff"/>
          <circle cx="7.1" cy="11" r="0.4" fill="#2E3A44"/>
          <!-- spout -->
          <path d="M9.5 6.2c.2-.8.7-1.5 1.5-1.7.9-.2 1.7.3 2 1.1.3.8-.1 1.7-.9 2-.8.3-1.5-.1-1.9-.8" fill="#BEE6FF" opacity="0.9"/>
        </svg>

      </button>
      <button 
        class="image-btn rabbit-hop"
        class:hopping={isHopping}
        class:floating={rabbitHoverCount > 0 && rabbitHoverCount < 5}
        style={rabbitHoverCount > 0 && rabbitHoverCount < 5 ? `position: fixed; left: ${rabbitPosition.x}px; top: ${rabbitPosition.y}px; z-index: 1000;` : ''}
        on:mouseenter={handleRabbitHover}
        on:click={() => handleAnimalClick('rabbit')} 
        aria-label="Rabbit"
      >
        <!-- Rabbit SVG -->
        <svg width="36" height="36" viewBox="0 0 24 24" fill="none" aria-hidden="true">
          <title>Rabbit</title>
          <!-- Left ear -->
          <ellipse cx="9" cy="7" rx="2.5" ry="5" fill="#fff"/>
          <!-- Right ear -->
          <ellipse cx="15" cy="7" rx="2.5" ry="5" fill="#fff"/>
          <!-- Head/body -->
          <circle cx="12" cy="14" r="6" fill="#fff"/>
          <!-- Left eye -->
          <circle cx="10" cy="13" r="0.8" fill="#333"/>
          <!-- Right eye -->
          <circle cx="14" cy="13" r="0.8" fill="#333"/>
          <!-- Nose -->
          <circle cx="12" cy="15" r="0.6" fill="#ffc0cb"/>
          <!-- Mouth -->
          <path d="M12 15.5 Q10.5 16.5 9.5 16 M12 15.5 Q13.5 16.5 14.5 16" stroke="#333" stroke-width="0.5" fill="none" stroke-linecap="round"/>
        </svg>
      </button>
      <button class="image-btn" on:click={() => handleAnimalClick('bird')} aria-label="Bird">
        <!-- Bird SVG -->
        <svg width="48" height="48" viewBox="0 0 24 24" fill="none" aria-hidden="false" role="img" xmlns="http://www.w3.org/2000/svg">
          <title>Bird</title>
          <desc>Stylized yellow bird with wing and dark eye</desc>
          <defs>
            <linearGradient id="gBird" x1="0" x2="1" y1="0" y2="1">
              <stop offset="0%" stop-color="#FFE38A"/>
              <stop offset="100%" stop-color="#FFCF45"/>
            </linearGradient>
          </defs>

          <!-- body -->
          <path d="M3 12c2-4 8-6 12-5 0 0-1 1-1 3s2 3 4 4c0 0-4 3-9 3S2 17 3 12z" fill="url(#gBird)"/>
          <!-- wing -->
          <path d="M9.5 11.3c1-1 2.6-1.1 3.8-.3-.6.9-1.4 1.6-2.4 2-1-.5-1.7-1.1-1.4-1.7z" fill="#FFD86B" opacity="0.92"/>
          <!-- beak -->
          <path d="M15.1 10.4c.4-.1.9.1 1.1.5.2.4.1.9-.3 1.1l-1-1.6z" fill="#FF9E3A"/>
          <!-- eye -->
          <circle cx="9" cy="11" r="0.8" fill="#222"/>
        </svg>
      </button>
  </div>
</section>

<style>
  .image-actions { 
    display:flex; 
    gap:25px; 
    margin-top:16px; 
    justify-content:center;
    position: relative;
  }
  .image-btn {
    width:80px;
    height:80px;
    border-radius:12px; 
    border:1px solid rgba(15,23,42,0.06);
    display:inline-flex; 
    align-items:center; 
    justify-content:center; 
    cursor:pointer;
    transition: transform .14s ease, box-shadow .14s ease, order .3s ease;
    padding:8px;
    background-color: lightcoral;
  }
  
  .image-btn.hopping {
    animation: quickHop 0.5s cubic-bezier(0.36, 0, 0.66, -0.56);
  }
  
  .image-btn.floating {
    transition: left 0.6s cubic-bezier(0.34, 1.56, 0.64, 1), 
                top 0.6s cubic-bezier(0.34, 1.56, 0.64, 1);
    cursor: default;
    box-shadow: 0 12px 32px rgba(233, 30, 99, 0.3);
  }

  @keyframes quickHop {
    0% { transform: translateY(0) scale(1); }
    25% { transform: translateY(-30px) scale(1.1) rotate(-5deg); }
    50% { transform: translateY(-20px) scale(1.05) rotate(5deg); }
    75% { transform: translateY(-25px) scale(1.08) rotate(-3deg); }
    100% { transform: translateY(0) scale(1) rotate(0deg); }
  }

  .image-btn:hover { transform: translateY(-4px); box-shadow: 0 10px 24px rgba(16,24,40,0.08); }
  .image-btn:active { transform: translateY(-1px) scale(.99); }
  .image-btn svg { display:block; width:50px; height:50px; }

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
