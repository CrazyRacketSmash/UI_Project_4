<script>
  import { createEventDispatcher, onMount } from 'svelte';
  const dispatch = createEventDispatcher();

  let notes = '';
  let saved = false;

  onMount(() => {
    try { notes = localStorage.getItem('bird_notes') || ''; saved = !!notes; } catch(e){ notes = ''; saved = false; }
  });

  function save() {
    try { localStorage.setItem('bird_notes', notes || ''); saved = true; } catch(e){}
    dispatch('save', notes);
  }
  function back() {
    dispatch('back');
  }

  // activity cards data (left: image/icon, right: description)
  const activities = [
    {
      id: 'letter',
      title: 'Write a letter',
      img: '📩',
      desc: "Write a letter to yourself to celebrate the good small things that happened this week."
    },
    {
      id: 'photo-walk',
      title: 'Photo walk',
      img: '📷',
      desc: "Take a short 15-minute walk and snap a few photos of things that make you pause."
    },
    {
      id: 'breath',
      title: 'Mini breath',
      img: '💨',
      desc: "Do a 3-minute breathing pause: inhale 4, hold 2, exhale 6, repeat 4 times."
    }
  ];
</script>

<div class="animal-page" style="padding:18px;">
  <div style="display:flex; gap:12px; align-items:center; margin-bottom:12px;">
    <div class="animal-icon" aria-hidden="true" style="width:56px; height:56px; display:flex; align-items:center; justify-content:center; border-radius:12px; background:linear-gradient(90deg,#FFED9A,#FFD773); position:relative;">
      <!-- bird svg -->
      <svg width="36" height="36" viewBox="0 0 24 24" fill="none" aria-hidden="true">
        <path d="M3 12c2-2 6-3 10-3s8 1 10 3c0 0-1 4-6 6-3 1-6 1-10 0C3 18 2 14 2 12z" fill="#fff"/>
      </svg>
    </div>

    <div style="flex:1;">
      <div style="font-weight:700; font-size:1.05rem; margin-bottom:6px;">Bird — Gentle reflection</div>
      <div class="small" style="color:#444;">
        This space allows you to spend some time on yourself and reflect on your thoughts and feelings.
      </div>
    </div>
  </div>
  <!-- animated flying bird (positioned to the right) -->
      <!-- <div class="flying-bird" aria-hidden="true">
        <svg viewBox="0 0 24 24" width="28" height="28" class="fb">
          <path d="M2 12c2-3 7-6 12-6 0 0-1 2-1 3 0 2 3 3 5 4 0 0-4 2-9 2s-9-2-7-5z" fill="#fff"/>
          <path d="M7 8c0 .8-1 1.5-1 1.5s1 .5 2 0c.5-.3 0-1.5-1-1.5z" fill="#e04"/>
        </svg>
      </div> -->
  <!-- activity cards list -->
  <div style="margin-top:20px;">
    <div class="activities">
      {#each activities as a}
        <div class="activity-card" role="article" aria-labelledby={"act-"+a.id}>
          <div class="card-image" aria-hidden="true">
            <!-- simple image area (emoji/SVG) -->
            <div class="img-emoji">{a.img}</div>
          </div>
          <div class="card-content">
            <div id={"act-"+a.id} class="card-title">{a.title}</div>
            <div class="card-desc">{a.desc}</div>
          </div>
        </div>
      {/each}
    </div>
  </div>
</div>


<div style="display:flex; gap:8px; margin-top:12px; align-items:center;">
    <button class="btn btn-primary" on:click={save}>Save</button>
    <button class="btn btn-ghost" on:click={back}>Back</button>
    <div style="flex:1"></div>
    {#if saved}
      <div class="small" style="color:var(--success)">Saved</div>
    {/if}
</div>

<style>
  .small { font-size:0.9rem; color:#6b7280; }

  /* flying bird animation area */
  .animal-icon { overflow:visible; }
  .flying-bird { position:absolute; right:-36px; top:6px; width:56px; height:56px; pointer-events:none; }
  .flying-bird .fb { animation: fly 3.6s linear infinite; transform-origin:center; }
  @keyframes fly {
    0% { transform: translateX(0) translateY(0) rotate(0deg); opacity:0; }
    10% { opacity:1; }
    50% { transform: translateX(40px) translateY(-6px) rotate(8deg); opacity:1; }
    90% { opacity:1;}
    100% { transform: translateX(80px) translateY(8px) rotate(-8deg); opacity:0; }
  }

  /* activities list */
  .activities { display:flex; flex-direction:column; gap:12px; margin-top:8px; }
  .activity-card {
    display:flex;
    gap:12px;
    align-items:flex-start;
    padding:12px;
    border-radius:10px;
    background: linear-gradient(180deg,#fff,#fbfdff);
    border:1px solid rgba(15,23,42,0.04);
    box-shadow: 0 6px 18px rgba(16,24,40,0.04);
  }
  .card-image { flex: 0 0 30%; display:flex; align-items:center; justify-content:center; }
  .card-content { flex: 1 1 70%; display:flex; flex-direction:column; }
  .img-emoji { font-size:28px; width:100%; text-align:center; }
  .card-title { font-weight:700; margin-bottom:6px; }
  .card-desc { color:var(--muted); font-size:0.95rem; }

  @media (max-width:720px) {
    .activity-card { flex-direction:row; }
    .card-image { flex-basis:30%; }
  }
</style>
