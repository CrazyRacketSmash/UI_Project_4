<script>
  import { createEventDispatcher } from 'svelte';
  const dispatch = createEventDispatcher();

  const activities = [
    { id: 1, emoji: '💬', title: 'Chat with a friend', desc: 'Send a quick "thinking of you" message to someone you care about or haven\'t kept in touch for a while.' },
    { id: 2, emoji: '🎨', title: 'Creative moment', desc: 'Spend several minutes for drawing, writing, creating something small, or build something by your own hands.' },
    { id: 3, emoji: '🚶', title: 'Nature walk', desc: 'Take a short or a long walk outside, enjoy the sights, and immerse in the nature around you.' },
    { id: 4, emoji: '📞', title: 'Family chat', desc: 'Pick up a phone and call a family member for a quick life checkin.' },
    { id: 5, emoji: '🎵', title: 'Dance to music', desc: 'Play your favorite song and dance freely. Keep adding more of your favorite songs.' },
    { id: 6, emoji: '📚', title: 'Read books', desc: 'Spend 15 minutes reading several pages of your favorite book or a book you\'ve never read before.' },
    { id: 7, emoji: '☕', title: 'Cozy moment', desc: 'Make a warm drink and enjoy a quiet moment with yourself.' },
    { id: 8, emoji: '🤝', title: 'Help someone', desc: 'Do a small act of kindness for someone — it connects us.' }
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
    <div class="activity-display">
      {#if spinResult}
        <div class="activity-card">
          <div class="activity-emoji">{spinResult.emoji}</div>
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
  }

  .activity-card {
    text-align:center;
    width:100%;
  }

  .activity-emoji {
    font-size:3.5rem;
    margin-bottom:12px;
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
