<script>
  import { createEventDispatcher, onMount } from 'svelte';
  const dispatch = createEventDispatcher();

  const activities = [
    { id: 1, emoji: '😢', title: 'Allow yourself to cry', desc: 'Give yourself permission to feel and release emotions. Crying is healthy and healing.' },
    { id: 2, emoji: '📝', title: 'Write your feelings', desc: 'Journal about what you\'re feeling without judgment. Let the words flow freely.' },
    { id: 3, emoji: '🎵', title: 'Listen to comforting music', desc: 'Play songs that resonate with your emotions or bring you peace.' },
    { id: 4, emoji: '🚶', title: 'Gentle movement', desc: 'Take a slow walk, do gentle stretches, or move your body mindfully.' },
    { id: 5, emoji: '💬', title: 'Talk to someone', desc: 'Reach out to a trusted friend, family member, or therapist to share how you feel.' },
    { id: 6, emoji: '🛀', title: 'Self-care ritual', desc: 'Take a warm bath, make tea, or do something nurturing for yourself.' },
    { id: 7, emoji: '🌅', title: 'Watch nature', desc: 'Sit outside or by a window and observe nature — the sky, trees, or birds.' },
    { id: 8, emoji: '📚', title: 'Read something uplifting', desc: 'Read poetry, quotes, or a book that brings comfort or inspiration.' }
  ];

  let selectedActivities = [];

  onMount(() => {
    try {
      const stored = JSON.parse(localStorage.getItem('sad_healing_activities') || '[]');
      selectedActivities = stored;
    } catch(e){}
  });

  function toggleActivity(id) {
    if (selectedActivities.includes(id)) {
      selectedActivities = selectedActivities.filter(a => a !== id);
    } else {
      selectedActivities = [...selectedActivities, id];
    }
    persist();
  }

  function persist() {
    try {
      localStorage.setItem('sad_healing_activities', JSON.stringify(selectedActivities));
    } catch(e){}
  }

  function finish() {
    dispatch('done');
  }
</script>

<div>
  <h3 style="margin:0 0 8px 0">Healing Activities</h3>
  <p class="small">Choose activities that feel right for you right now. It's okay to take time to heal.</p>

  <div class="activities-grid">
    {#each activities as activity}
      <button
        class="activity-card"
        class:selected={selectedActivities.includes(activity.id)}
        on:click={() => toggleActivity(activity.id)}
      >
        <div class="card-header">
          <div class="activity-emoji">{activity.emoji}</div>
          {#if selectedActivities.includes(activity.id)}
            <span class="check-badge">✓</span>
          {/if}
        </div>
        <div class="activity-title">{activity.title}</div>
        <div class="activity-desc">{activity.desc}</div>
      </button>
    {/each}
  </div>

  <div style="display:flex; gap:8px; margin-top:16px; justify-content:flex-end;">
    <button class="btn btn-ghost" on:click={finish}>Done</button>
  </div>
</div>

<style>
  .small { font-size:0.9rem; color:#6b7280; }

  .activities-grid {
    display:grid;
    grid-template-columns: repeat(auto-fill, minmax(280px, 1fr));
    gap:12px;
    margin-top:12px;
  }

  .activity-card {
    background: linear-gradient(180deg, #fff, #fef8fb);
    border:1px solid rgba(219,39,119,0.08);
    border-radius:12px;
    padding:16px;
    text-align:left;
    cursor:pointer;
    transition: transform .12s ease, box-shadow .12s ease, border-color .12s ease;
    position:relative;
  }

  .activity-card:hover {
    transform:translateY(-2px);
    box-shadow: 0 10px 24px rgba(219,39,119,0.08);
  }

  .activity-card.selected {
    border-color: rgba(219,39,119,0.24);
    background: linear-gradient(180deg, #fef8fb, #fff);
    box-shadow: 0 10px 24px rgba(219,39,119,0.12);
  }

  .card-header {
    display:flex;
    justify-content:space-between;
    align-items:flex-start;
    margin-bottom:8px;
  }

  .activity-emoji {
    font-size:2rem;
  }

  .check-badge {
    background: linear-gradient(90deg,#ec4899,#f472b6);
    color:white;
    font-weight:700;
    border-radius:8px;
    padding:4px 8px;
    font-size:0.85rem;
    line-height:1;
  }

  .activity-title {
    font-weight:700;
    font-size:1rem;
    margin-bottom:6px;
    color:#831843;
  }

  .activity-desc {
    font-size:0.9rem;
    color:#6b7280;
    line-height:1.4;
  }

  .btn {
    padding:10px 16px;
    border-radius:8px;
    border:none;
    cursor:pointer;
    font-weight:600;
  }

  .btn-ghost {
    background:transparent;
    border:1px solid rgba(0,0,0,0.06);
    color:#0f172a;
  }

  .btn-ghost:hover {
    transform:translateY(-2px);
  }

  @media (max-width: 720px) {
    .activities-grid {
      grid-template-columns: 1fr;
    }
  }
</style>
