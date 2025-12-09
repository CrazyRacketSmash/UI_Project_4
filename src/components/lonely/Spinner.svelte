<script>
  import { createEventDispatcher } from 'svelte';
  const dispatch = createEventDispatcher();

  const activities = [
    { id: 1, icon: 'chat', title: 'Chat with a friend', desc: 'Send a quick "thinking of you" message to someone you care about or haven\'t kept in touch for a while.' },
    { id: 2, icon: 'create', title: 'Try new things', desc: 'Spend several minutes for creating something small, build something new by your own hands, or try out things you\'ve never done before.' },
    { id: 3, icon: 'walk', title: 'Nature walk', desc: 'Take a short or a long walk outside, enjoy the sights, and immerse in the nature around you.' },
    { id: 4, icon: 'call', title: 'Family chat', desc: 'Pick up a phone and call a family member for a quick life checkin.' },
    { id: 5, icon: 'dance', title: 'Dance to music', desc: 'Play your favorite song and dance freely. Keep adding more of your favorite songs.' },
    { id: 6, icon: 'watch', title: 'Watch your favorite Netflix series or movie', desc: 'Spend some time watching your favorite Netflix series or movie.' },
    { id: 7, icon: 'coffee', title: 'Cozy moment', desc: 'Make a warm drink and enjoy a quiet moment with yourself.' },
    { id: 8, icon: 'help', title: 'Help someone', desc: 'Do a small act of kindness for someone...it connects us.' }
  ];

  let spinResult = null;
  let isSpinning = false;

  function spin() {
    if (isSpinning) return;
    isSpinning = true;
    
    // animate multiple spins
    let count = 0;
    const spinInterval = setInterval(() => {
      spinResult = activities[Math.floor(Math.random() * activities.length)];
      count++;
      if (count > 15) {
        clearInterval(spinInterval);
        isSpinning = false;
      }
    }, 80);
  }

  function finish() {
    dispatch('done');
  }
</script>

<div>
  <h3 style="margin:0 0 8px 0">Activity Spinner</h3>
  <p class="small">Spin the wheel to get a random activity suggestion. Pick one that resonates with you!</p>

  <div class="spinner-container">
    <!-- Rising bubbles background -->
    <div class="bubbles-bg" aria-hidden="true">
      <div class="bubble bubble-1"></div>
      <div class="bubble bubble-2"></div>
      <div class="bubble bubble-3"></div>
      <div class="bubble bubble-4"></div>
      <div class="bubble bubble-5"></div>
      <div class="bubble bubble-6"></div>
      <div class="bubble bubble-7"></div>
    </div>

    <div class="activity-display">
      {#if spinResult}
        <div class="activity-card">
          <div class="activity-icon">
            <svg viewBox="0 0 64 64" width="64" height="64" aria-hidden="true">
              {#if spinResult.icon === 'chat'}
                <circle cx="32" cy="32" r="30" fill="url(#grad-chat)"/>
                <path d="M20 20h24v20H28l-4 6v-6h-4z" fill="white"/>
                <defs><linearGradient id="grad-chat" x1="0%" y1="0%" x2="100%" y2="100%"><stop offset="0%" style="stop-color:#6aa6ff;stop-opacity:1" /><stop offset="100%" style="stop-color:#7ee3ff;stop-opacity:1" /></linearGradient></defs>
              {:else if spinResult.icon === 'create'}
                <circle cx="32" cy="32" r="30" fill="url(#grad-create)"/>
                <path d="M26 28c0-3.3 2.7-6 6-6s6 2.7 6 6c0 2-1 3.8-2.5 4.8V36h-7v-3.2C27 31.8 26 30 26 28z" fill="white"/>
                <rect x="29" y="36" width="6" height="3" rx="1" fill="white"/>
                <rect x="30" y="39" width="4" height="2" rx="1" fill="white"/>
                <line x1="32" y1="17" x2="32" y2="20" stroke="white" stroke-width="2" stroke-linecap="round" class="s-NWm_6TrhaFd3"></line>
                <line x1="22" y1="22" x2="24.5" y2="24.5" stroke="white" stroke-width="2" stroke-linecap="round" class="s-NWm_6TrhaFd3"></line>
                <line x1="42" y1="22" x2="39.5" y2="24.5" stroke="white" stroke-width="2" stroke-linecap="round" class="s-NWm_6TrhaFd3"></line>
                <defs><linearGradient id="grad-create" x1="0%" y1="0%" x2="100%" y2="100%"><stop offset="0%" style="stop-color:#ec4899;stop-opacity:1" /><stop offset="100%" style="stop-color:#f472b6;stop-opacity:1" /></linearGradient></defs>
              {:else if spinResult.icon === 'walk'}
                <circle cx="32" cy="32" r="30" fill="url(#grad-walk)"/>
                <path d="M32 12 L38 22 L36 22 L42 32 L39 32 L44 42 L20 42 L25 32 L22 32 L28 22 L26 22 Z" fill="white"/>
                <rect x="30" y="42" width="4" height="8" fill="white"/>
                <defs><linearGradient id="grad-walk" x1="0%" y1="0%" x2="100%" y2="100%"><stop offset="0%" style="stop-color:#10b981;stop-opacity:1" /><stop offset="100%" style="stop-color:#34d399;stop-opacity:1" /></linearGradient></defs>
              {:else if spinResult.icon === 'call'}
                <circle cx="32" cy="32" r="30" fill="url(#grad-call)"/>
                <path d="M24 18h16v4h-16zM24 22h4v16h-4zM36 22h4v16h-4zM24 38h16v4h-16z" fill="white"/>
                <circle cx="32" cy="30" r="6" fill="none" stroke="white" stroke-width="2"/>
                <defs><linearGradient id="grad-call" x1="0%" y1="0%" x2="100%" y2="100%"><stop offset="0%" style="stop-color:#f59e0b;stop-opacity:1" /><stop offset="100%" style="stop-color:#fbbf24;stop-opacity:1" /></linearGradient></defs>
              {:else if spinResult.icon === 'dance'}
                <circle cx="32" cy="32" r="30" fill="url(#grad-dance)"/>
                <path d="M38 16v20c0 3-2 5-4 5s-4-2-4-5 2-5 4-5c1 0 2 0.5 2.5 1V16h1.5z" fill="white"/>
                <circle cx="34" cy="18" r="2" fill="white"/>
                <defs><linearGradient id="grad-dance" x1="0%" y1="0%" x2="100%" y2="100%"><stop offset="0%" style="stop-color:#a855f7;stop-opacity:1" /><stop offset="100%" style="stop-color:#d946ef;stop-opacity:1" /></linearGradient></defs>
              {:else if spinResult.icon === 'watch'}
                <circle cx="32" cy="32" r="30" fill="url(#grad-watch)"/>
                <rect x="18" y="20" width="28" height="24" rx="2" fill="none" stroke="white" stroke-width="2.5"/>
                <circle cx="32" cy="32" r="6" fill="white"/>
                <defs><linearGradient id="grad-watch" x1="0%" y1="0%" x2="100%" y2="100%"><stop offset="0%" style="stop-color:#06b6d4;stop-opacity:1" /><stop offset="100%" style="stop-color:#22d3ee;stop-opacity:1" /></linearGradient></defs>
              {:else if spinResult.icon === 'coffee'}
                <circle cx="32" cy="32" r="30" fill="url(#grad-coffee)"/>
                <path d="M22 20h16v16c0 2-2 4-4 4h-8c-2 0-4-2-4-4z" fill="white" stroke="white" stroke-width="2" stroke-linejoin="round"/>
                <path d="M39 25c2 -2 7 2 1 9" stroke="white" stroke-width="2.5" stroke-linecap="round" fill="none"/>
                <defs><linearGradient id="grad-coffee" x1="0%" y1="0%" x2="100%" y2="100%"><stop offset="0%" style="stop-color:#d97706;stop-opacity:1" /><stop offset="100%" style="stop-color:#f59e0b;stop-opacity:1" /></linearGradient></defs>
              {:else if spinResult.icon === 'help'}
                <circle cx="32" cy="32" r="30" fill="url(#grad-help)"/>
                <path d="M20 32c0-6.6 5.4-12 12-12s12 5.4 12 12-5.4 12-12 12-12-5.4-12-12z" fill="none" stroke="white" stroke-width="2.5"/>
                <path d="M28 28c0-2 2-3 3-3 2 0 3 1 3 3 0 3-2 4-1 6M32 39v2" stroke="white" stroke-width="2.5" stroke-linecap="round" fill="none"/>
                <defs><linearGradient id="grad-help" x1="0%" y1="0%" x2="100%" y2="100%"><stop offset="0%" style="stop-color:#ef4444;stop-opacity:1" /><stop offset="100%" style="stop-color:#f87171;stop-opacity:1" /></linearGradient></defs>
              {/if}
            </svg>
          </div>
          <div class="activity-title">{spinResult.title}</div>
          <div class="activity-desc">{spinResult.desc}</div>
        </div>
      {:else}
        <div class="activity-placeholder">
          <div style="font-size:2rem; margin-bottom:8px">🎡</div>
          <div style="color:#666; font-weight:600">Tap spin to get started</div>
        </div>
      {/if}
    </div>
  </div>

  <div style="display:flex; gap:8px; margin-top:16px; justify-content:center; flex-wrap:wrap;">
    <button class="btn btn-primary" on:click={spin} disabled={isSpinning}>
      {isSpinning ? 'Spinning...' : 'Spin'}
    </button>
    <button class="btn btn-ghost" on:click={finish}>Done</button>
  </div>
</div>

<style>
  .small { font-size:0.9rem; color:#6b7280; }
  
  .spinner-container {
    display:flex;
    justify-content:center;
    margin-top:12px;
    position:relative;
  }

  /* Bubbles container */
  .bubbles-bg {
    position:absolute;
    inset:0;
    pointer-events:none;
    overflow:hidden;
    border-radius:12px;
  }

  /* Enhanced bubble styling with gradient and glow */
  .bubble {
    position:absolute;
    bottom:-30px;
    border-radius:50%;
    opacity:0;
    filter: drop-shadow(0 8px 16px rgba(126,227,255,0.3));
  }

  /* Bubble gradients more visible and shimmery */
  .bubble-1 {
    width:28px;
    height:28px;
    left:8%;
    background: radial-gradient(circle at 35% 35%, rgba(255,255,255,0.8), rgba(126,227,255,0.6), rgba(14,165,233,0.4));
    box-shadow: inset -2px -2px 6px rgba(14,165,233,0.3), inset 2px 2px 6px rgba(255,255,255,0.7), 0 12px 24px rgba(79,156,232,0.2);
    animation: rise 3.2s ease-out infinite;
  }

  .bubble-2 {
    width:36px;
    height:36px;
    left:25%;
    background: radial-gradient(circle at 32% 32%, rgba(255,255,255,0.75), rgba(126,227,255,0.55), rgba(14,165,233,0.35));
    box-shadow: inset -3px -3px 8px rgba(14,165,233,0.25), inset 3px 3px 8px rgba(255,255,255,0.8), 0 14px 28px rgba(79,156,232,0.25);
    animation: rise 3.8s ease-out 0.4s infinite;
  }

  .bubble-3 {
    width:22px;
    height:22px;
    left:42%;
    background: radial-gradient(circle at 38% 38%, rgba(255,255,255,0.85), rgba(126,227,255,0.65), rgba(14,165,233,0.45));
    box-shadow: inset -2px -2px 5px rgba(14,165,233,0.35), inset 2px 2px 5px rgba(255,255,255,0.75), 0 10px 20px rgba(79,156,232,0.18);
    animation: rise 3.5s ease-out 0.8s infinite;
  }

  .bubble-4 {
    width:32px;
    height:32px;
    left:58%;
    background: radial-gradient(circle at 35% 35%, rgba(255,255,255,0.8), rgba(126,227,255,0.6), rgba(14,165,233,0.38));
    box-shadow: inset -3px -3px 7px rgba(14,165,233,0.28), inset 3px 3px 7px rgba(255,255,255,0.8), 0 13px 26px rgba(79,156,232,0.22);
    animation: rise 4s ease-out 0.2s infinite;
  }

  .bubble-5 {
    width:18px;
    height:18px;
    left:74%;
    background: radial-gradient(circle at 40% 40%, rgba(255,255,255,0.9), rgba(126,227,255,0.7), rgba(14,165,233,0.5));
    box-shadow: inset -1px -1px 4px rgba(14,165,233,0.4), inset 1px 1px 4px rgba(255,255,255,0.85), 0 9px 18px rgba(79,156,232,0.15);
    animation: rise 3.4s ease-out 1.1s infinite;
  }

  .bubble-6 {
    width:26px;
    height:26px;
    left:32%;
    background: radial-gradient(circle at 36% 36%, rgba(255,255,255,0.82), rgba(126,227,255,0.62), rgba(14,165,233,0.42));
    box-shadow: inset -2px -2px 6px rgba(14,165,233,0.32), inset 2px 2px 6px rgba(255,255,255,0.78), 0 11px 22px rgba(79,156,232,0.2);
    animation: rise 3.7s ease-out 0.6s infinite;
  }

  .bubble-7 {
    width:20px;
    height:20px;
    left:68%;
    background: radial-gradient(circle at 38% 38%, rgba(255,255,255,0.88), rgba(126,227,255,0.68), rgba(14,165,233,0.48));
    box-shadow: inset -2px -2px 5px rgba(14,165,233,0.36), inset 2px 2px 5px rgba(255,255,255,0.82), 0 10px 20px rgba(79,156,232,0.17);
    animation: rise 3.9s ease-out 0.9s infinite;
  }

  @keyframes rise {
    0% {
      transform: translateY(0) translateX(0) scale(0.75) rotate(0deg);
      opacity: 0;
    }
    5% {
      opacity: 0.7;
    }
    100% {
      transform: translateY(-420px) translateX(25px) scale(0.85) rotate(180deg);
      opacity: 0;
    }
  }

  .activity-display {
    width:100%;
    max-width:420px;
    min-height:280px;
    display:flex;
    align-items:center;
    justify-content:center;
    border-radius:12px;
    background: linear-gradient(180deg, rgba(14,165,233,0.08), rgba(126,227,255,0.04));
    border:2px solid rgba(14,165,233,0.12);
    padding:20px;
    position:relative;
    z-index:1;
  }

  .activity-card {
    text-align:center;
    width:100%;
  }

  .activity-icon {
    display: flex;
    align-items: center;
    justify-content: center;
    margin-bottom: 16px;
  }

  .activity-icon svg {
    filter: drop-shadow(0 8px 16px rgba(0,0,0,0.12));
  }

  .activity-title {
    font-weight:700;
    font-size:1.15rem;
    margin-bottom:8px;
    color:#064e6b;
  }

  .activity-desc {
    font-size:0.95rem;
    color:#6b7280;
    line-height:1.5;
  }

  .activity-placeholder {
    text-align:center;
    opacity:0.7;
  }

  .btn {
    padding:10px 16px;
    border-radius:8px;
    border:none;
    cursor:pointer;
    font-weight:600;
    transition: transform .12s ease;
  }

  .btn-primary {
    background: linear-gradient(90deg,#6aa6ff,#7ee3ff);
    color:white;
  }

  .btn-primary:hover:not(:disabled) {
    transform:translateY(-2px);
    box-shadow: 0 10px 24px rgba(79,156,232,0.12);
  }

  .btn-primary:disabled {
    opacity:0.6;
    cursor:not-allowed;
  }

  .btn-ghost {
    background:transparent;
    border:1px solid rgba(0,0,0,0.06);
    color:#0f172a;
  }

  .btn-ghost:hover {
    transform:translateY(-2px);
  }
</style>
