<script>
  import { createEventDispatcher, onMount, onDestroy } from 'svelte';
  const dispatch = createEventDispatcher();
  // Save notification state
  let showNotif = false;
  let notifMsg = '';
  let notifTimer = null;

  function showNotification(msg = 'Success', ms = 3000) {
    notifMsg = msg;
    showNotif = true;
    if (notifTimer) clearTimeout(notifTimer);
    notifTimer = setTimeout(() => {
      showNotif = false;
      notifTimer = null;
    }, ms);
  }
  onDestroy(() => { if (notifTimer) clearTimeout(notifTimer); });

  // three journal cards
  let cards = [
    { id: 1, subject: 'Daily Journal', text: '', flipped: false },
    { id: 2, subject: 'Thoughts', text: '', flipped: false },
    { id: 3, subject: 'Gratitude', text: '', flipped: false }
  ];

  // human-friendly descriptions for each subject (shown beneath subject)
  const subjectDescriptions = {
    'Daily Journal': 'Let\'s capture any kind of activities, moods, and moments you\'ve experienced lately.',
    'Thoughts': 'Put down your overwhelming thoughts, worries or life issues here.',
    'Gratitude': 'Reflect great things that happened to you the past few weeks and what you\'re thankful for.'
  };

  let saved = false;

  onMount(() => {
    try {
      const stored = JSON.parse(localStorage.getItem('whale_cards') || 'null');
      if (stored && Array.isArray(stored) && stored.length) {
        cards = stored.map((c, idx) => idx === 0 || idx === 1 || idx === 2 ? { ...c, flipped: false } : c);
      }
      saved = cards.some(c => c.text && c.text.length);
    } catch (e) {
      // ignore
    }
  });

  function flip(i) {
    cards[i].flipped = !cards[i].flipped;
    // reassign to trigger reactivity
    cards = cards;
  }

  function updateText(i, v) {
    cards[i].text = v;
    cards = cards;
  }

  function saveAll() {
    try {
      localStorage.setItem('whale_cards', JSON.stringify(cards));
      saved = true;
    } catch (e) {}
    dispatch('save', cards);
    showNotification('Success');
  }
  function back() {
    dispatch('back');
  }
</script>

<div class="whale-wrap">
  <div class="whale-header">
    <div class="whale-icon" aria-hidden="true">
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
    </div>
    <div class="whale-title">
      <div class="h3">Whale</div>
      <div class="small">Use these cards to capture short reflections. Flip a card to write your journal entry.</div>
    </div>
  </div>
  <br>
  <div class="cards-grid" role="list">
    {#each cards as card, i}
      <div class="card color-{card.id}" role="listitem">
        <div class="card-inner" class:flipped={card.flipped}>
          <!-- front -->
          <div class="card-face card-front">
            <div class="card-top">
            <div class="card-subject" aria-hidden="true">{card.subject}</div>
              <button class="flip-btn" on:click={() => flip(i)} aria-label="Flip card">✧</button>
            </div>
            <div class="card-body">
              <div class="card-desc small">{subjectDescriptions[card.subject]}</div>
              <div style="height:8px"></div>
               <div class="small muted">Tap the flip icon to write your journal</div>
             </div>
           </div>

          <!-- back -->
          <div class="card-face card-back">
            <div class="card-top">
              <div style="font-weight:600;">{card.subject}</div>
              <button class="flip-btn" on:click={() => flip(i)} aria-label="Flip card back">↺</button>
            </div>
            <textarea class="card-text" bind:value={card.text} placeholder="Write your entry..." on:input={(e) => updateText(i, e.target.value)}></textarea>
          </div>
        </div>
      </div>
    {/each}
  </div>
  <br>
  <div class="whale-actions">
    <button class="btn btn-ghost" on:click={back}>Back</button>
    <div style="flex:1"></div>
    <button class="btn btn-primary" on:click={saveAll}>Save All</button>
  </div>

  <!-- slide-in save notification -->
  <div class="save-notif" role="status" aria-live="polite" class:visible={showNotif}>
    <span class="checkmark" aria-hidden="true">
      <svg viewBox="0 0 52 52" width="20" height="20" focusable="false" aria-hidden="true">
        <circle cx="26" cy="26" r="24" fill="none" stroke="rgba(255,255,255,0.14)" stroke-width="2"/>
        <path class="checkmark-path" fill="none" stroke="white" stroke-width="4" d="M14 27l8 8 16-16" stroke-linecap="round" stroke-linejoin="round"/>
      </svg>
    </span>
    <span class="notif-text">{notifMsg}</span>
  </div>
</div>

<style>
  .whale-wrap { position: relative; padding:8px 0; }
  .whale-header { display:flex; gap:12px; align-items:center; margin-bottom:12px; }
  .whale-icon { width:56px; height:56px; display:flex; align-items:center; justify-content:center; border-radius:12px; background:linear-gradient(90deg,#6aa6ff,#7ee3ff); }
  .whale-title .h3 { font-weight:700; font-size:1.05rem; margin-bottom:4px; }
  .small { font-size:0.9rem; color:#6b7280; }

  .cards-grid {
    display:flex;
    gap:12px;
    align-items:stretch;
    margin-top:8px;
    margin-bottom:12px;
  }

  /* card base */
  .card { width: 100%; max-width: 340px; perspective: 1000px; }
  .card-inner {
    position: relative;
    width: 100%;
    min-height: 220px;
    transform-style: preserve-3d;
    transition: transform .6s cubic-bezier(.2,.9,.34,1);
  }
  .card-inner.flipped { transform: rotateY(180deg); }

  .card-face {
    position: absolute;
    inset: 0;
    backface-visibility: hidden;
    border-radius: 12px;
    padding: 12px;
    box-shadow: 0 8px 20px rgba(16,24,40,0.06);
    background: linear-gradient(180deg,#fff,#fbfdff);
    display:flex;
    flex-direction:column;
  }

  .card-front { z-index:2; }
  .card-back { transform: rotateY(180deg); }

  .card-top { display:flex; align-items:center; justify-content:space-between; gap:8px; margin-bottom:8px; }
  .card-subject { font-weight:700; font-size:1rem; }
  .card-desc { color:var(--muted); margin-top:6px; font-size:0.9rem; }

  .flip-btn {
    background:transparent;
    border:none;
    cursor:pointer;
    font-size:1.05rem;
    padding:6px;
    border-radius:8px;
  }

  .card-text {
    flex:1;
    width:100%;
    resize:vertical;
    padding:8px;
    border-radius:8px;
    border:1px solid rgba(15,23,42,0.06);
    font-size:0.95rem;
    margin-top:6px;
  }

  .whale-actions { display:flex; gap:8px; align-items:center; margin-top:8px; }

  /* save notification (slides in from right) */
  .save-notif {
    position: absolute;
    right: 18px;
    top: 18px;
    transform: translateX(120%);
    opacity: 0;
    background: linear-gradient(90deg,#8ad47e,#6bec78);
    color: white;
    padding: 10px 14px;
    border-radius: 10px;
    box-shadow: 0 12px 40px rgba(2,6,23,0.18);
    transition: transform .36s cubic-bezier(.2,.9,.34,1), opacity .28s ease;
    z-index: 80;
    font-weight:600;
    pointer-events: none;
    display: inline-flex;
    align-items: center;
    gap: 10px;
  }
  .save-notif.visible {
    transform: translateX(0);
    opacity: 1;
    pointer-events: auto;
  }

  /* checkmark visuals + draw animation */
  .checkmark svg { display:block; }
  .checkmark-path {
    stroke-dasharray: 48;
    stroke-dashoffset: 48;
    transition: stroke-dashoffset .42s ease-out;
  }
  .save-notif.visible .checkmark-path {
    stroke-dashoffset: 0;
  }

  .notif-text { font-weight:700; }

  /* ensure whale container has positioning context */
  .whale-wrap { position: relative; padding:8px 0; }

  /* per-card color themes */
  .card.color-1 .card-face.card-front { background: linear-gradient(180deg,#f7e6dd,#ffe9e0); color:#331a12; }
  .card.color-1 .card-face.card-back  { background: linear-gradient(180deg,#ffd6ca,#ffb3a8); color:#2a0f06; }

  .card.color-2 .card-face.card-front { background: linear-gradient(180deg,#e1faed,#e0fff0); color:#07321f; }
  .card.color-2 .card-face.card-back  { background: linear-gradient(180deg,#bff8dd,#80f0b8); color:#05341a; }

  .card.color-3 .card-face.card-front { background: linear-gradient(180deg,#e1ebfe,#e9f0ff); color:#072244; }
  .card.color-3 .card-face.card-back  { background: linear-gradient(180deg,#cfe2ff,#9fc7ff); color:#041c3a; }

  /* ensure buttons / text contrast on colored backgrounds */
  .card-subject, .card-desc, .card-top button, .card-top .flip-btn { color: inherit; }
  .card-text { background: rgba(255,255,255,0.9); color: #0f172a; }

  /* responsive */
  @media (max-width: 960px) {
    .cards-grid { flex-direction:column; }
    .card { max-width: 100%; }
  }
</style>
