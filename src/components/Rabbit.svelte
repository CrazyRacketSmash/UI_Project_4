<script>
  import { createEventDispatcher, onMount, onDestroy } from 'svelte';
  const dispatch = createEventDispatcher();

  // cards: two goal cards, one text card, one todo card
  // add completed:false on all so saved state is consistent
  let cards = [
    { id: 'nature', label: 'Nature', icon: '🌿', type: 'nature', nature: '', unit: 'minutes', completed: false },
    { id: 'exercise', label: 'Exercise', icon: '🏃', type: 'goal', goal: '', unit: 'minutes', completed: false },
    { id: 'kindness', label: 'Kindness', icon: '🤝', type: 'text', text: '', completed: false },
    { id: 'barehand', label: 'Bare Hand', icon: '👐', type: 'todo', todos: [], completed: false }
  ];

  let openCard = null; // card id opened in overlay
  let saved = false;
  // currently selected card for overlay
  $: currentCard = openCard ? cards.find(c => c.id === openCard) : null;

  onMount(() => {
    try {
      const stored = JSON.parse(localStorage.getItem('rabbit_cards') || 'null');
      if (stored && Array.isArray(stored) && stored.length) {
        // merge stored values but preserve type from default cards
        cards = cards.map(c => {
          const s = stored.find(x => x.id === c.id);
          if (s) {
            // Preserve type, icon, label from default; merge user data
            return { 
              ...c, 
              ...s, 
              type: c.type,
              icon: c.icon,
              label: c.label
            };
          }
          return c;
        });
      }
    } catch (e) { /* ignore */ }

    // persist on page unload to guard against accidental refresh/close
    function _beforeUnload() { persist(); }
    // attach listener on mount and remove on destroy
    window.addEventListener('beforeunload', _beforeUnload);
    return () => {
      window.removeEventListener('beforeunload', _beforeUnload);
    };
  });

  function openCardOverlay(id) {
    openCard = id;
  }

  function closeOverlay() {
    // ensure any in-progress edits are saved before closing
    persist();
    openCard = null;
  }

  function saveCard(id) {
    persist();
    saved = true;
    dispatch('save', cards);
    // small visual saved flag reset
    setTimeout(() => saved = false, 1800);
  }

  function persist() {
    try {
      localStorage.setItem('rabbit_cards', JSON.stringify(cards));
    } catch (e) {}
  }

  // helpers for todo list (barehand)
  function addTodo(card) {
    card.todos = card.todos || [];
    card.todos.push({ id: Date.now(), text: '', done: false });
    cards = cards;
    persist();
  }
  function removeTodo(card, idx) {
    card.todos.splice(idx, 1);
    cards = cards;
    persist();
  }

  // quick Save All
  function saveAll() {
    persist();
    saved = true;
    dispatch('save', cards);
    setTimeout(() => saved = false, 1800);
  }
  function back() {
    dispatch('back');
  }

  // Auto-completion for todo card
  $: {
    const todoCard = cards.find(c => c.type === 'todo');
    if (todoCard && Array.isArray(todoCard.todos)) {
      const allDone = todoCard.todos.length > 0 && todoCard.todos.every(t => t.done);
      if (todoCard.completed !== allDone) {
        todoCard.completed = allDone;
        cards = cards;
        persist();
      }
    }
  }

  // Auto-completion for text card (kindness)
  $: {
    const textCard = cards.find(c => c.type === 'text');
    if (textCard) {
      const hasText = (textCard.text || '').trim().length > 0;
      if (textCard.completed !== hasText) {
        textCard.completed = hasText;
        cards = cards;
        persist();
      }
    }
  }
</script>

<div class="animal-page" style="padding:18px;">
  <div style="display:flex; gap:12px; align-items:center; margin-bottom:12px;">
    <div class="animal-icon" aria-hidden="true" style="width:56px; height:56px; display:flex; align-items:center; justify-content:center; border-radius:12px; background:linear-gradient(90deg,#FFD7D7,#FFB6C1);">
      <!-- rabbit svg -->
      <svg width="36" height="36" viewBox="0 0 24 24" fill="none" aria-hidden="true"><path d="M9 4c-.5-1-2-2-3-1s-1 3 0 4 3 1 4 0 0-2-1-3z" fill="#fff"/><path d="M15 4c.5-1 2-2 3-1s1 3 0 4-3 1-4 0 0-2 1-3z" fill="#fff"/></svg>
    </div>

    <div style="flex:1;">
      <div style="font-weight:700; font-size:1.05rem; margin-bottom:6px;">Rabbit — Active Hopping</div>
      <div class="small" style="color:#444;">
        Pick a category card to set goals, jot small acts of kindness, or manage a quick to-do list. Keep hopping through them!
      </div>
    </div>
  </div>
  <br>
  <!-- Cards grid: 2 columns -->
  <div class="card-grid" role="list" aria-label="Rabbit categories">
    {#each cards as card}
      <button class="cat-card" on:click={() => openCardOverlay(card.id)} aria-haspopup="dialog" aria-label={card.label}>
        <div class="card-top-badge">
          {#if card.completed}
            <span class="tile-check" aria-hidden="true">✔︎</span>
          {/if}
        </div>
        <div class="cat-icon">{card.icon}</div>
        <div class="cat-label">{card.label}</div>
        {#if card.type === 'nature' && card.nature}
          <div class="cat-sub">Nature goal: {card.nature} {card.unit}</div>
        {/if}

        {#if card.type === 'goal' && card.goal}
          <div class="cat-sub">Goal: {card.goal} {card.unit}</div>
        {/if}

        {#if card.type === 'text' && card.text}
          <div class="cat-sub">{card.text.length > 40 ? card.text.slice(0,40) + '…' : card.text}</div>
        {/if}
        
        {#if card.type === 'todo' && card.todos && card.todos.length}
          <div class="cat-sub">{card.todos.filter(t=>t.done).length}/{card.todos.length} done</div>
        {/if}
      </button>
    {/each}
  </div>
  <br>
  <div style="display:flex; gap:8px; margin-top:12px; align-items:center;">
    <button class="btn btn-primary" on:click={saveAll}>Save All</button>
    <button class="btn btn-ghost" on:click={back}>Back</button>
    <div style="flex:1"></div>
    {#if saved}<div class="small" style="color:var(--success)">Saved</div>{/if}
  </div>
</div>

<!-- Overlay modal for card editors -->
{#if openCard}
  {#key openCard}
    <div class="overlay" role="dialog" aria-modal="true">
      <div class="modal">
        <!-- header -->
        {#if currentCard}
          <div style="display:flex; align-items:center; gap:12px;">
            <div style="font-size:28px">{currentCard.icon}</div>
            <div>
              <div style="font-weight:700; font-size:1.05rem;">{currentCard.label}</div>
              <div class="small" style="color:#666">
                {#if currentCard.type === 'nature'}Set a time for immersion in nature.{/if}
                {#if currentCard.type === 'goal'}Lock in a specific time target for exercising.{/if}
                {#if currentCard.type === 'text'}Write a short kindness note.{/if}
                {#if currentCard.type === 'todo'}Create fun and small things you can do with bare hands.{/if}
              </div>
            </div>
          </div>

          <div style="margin-top:12px;">
            {#if currentCard.type === 'nature'}
              <!-- svelte-ignore a11y-label-has-associated-control -->
              <label class="small">Target</label>
              <div style="display:flex; gap:8px; margin-top:6px;">
                <input type="number" min="0" bind:value={currentCard.nature} on:input={() => persist()} placeholder="e.g. 30" style="padding:8px; width:120px;"/>
                <select bind:value={currentCard.unit} on:change={() => persist()} style="padding:8px;">
                  <option value="minutes">minutes</option>
                </select>
              </div>
              <div style="margin-top:10px; display:flex; align-items:center; gap:8px;">
                <input id="nature-complete" type="checkbox" bind:checked={currentCard.completed} on:change={() => { cards = cards; persist(); }} />
                <label for="nature-complete" class="small">Mark complete</label>
              </div>
            {/if}
            {#if currentCard.type === 'goal'}
              <!-- svelte-ignore a11y-label-has-associated-control -->
              <label class="small">Target</label>
              <div style="display:flex; gap:8px; margin-top:6px;">
                <input type="number" min="0" bind:value={currentCard.goal} on:input={() => persist()} placeholder="e.g. 30" style="padding:8px; width:120px;"/>
                <select bind:value={currentCard.unit} on:change={() => persist()} style="padding:8px;">
                  <option value="minutes">minutes</option>
                  <option value="reps">reps</option>
                  <option value="times">times</option>
                </select>
              </div>
              <div style="margin-top:10px; display:flex; align-items:center; gap:8px;">
                <input id="exercise-complete" type="checkbox" bind:checked={currentCard.completed} on:change={() => { cards = cards; persist(); }} />
                <label for="exercise-complete" class="small">Mark complete</label>
              </div>
            {/if}

            {#if currentCard.type === 'text'}
              <!-- svelte-ignore a11y-label-has-associated-control -->
              <label class="small">Kindness note</label>
              <textarea bind:value={currentCard.text} on:input={() => persist()} rows="6" style="width:100%; margin-top:6px; padding:8px;"></textarea>
            {/if}

            {#if currentCard.type === 'todo'}
              <!-- svelte-ignore a11y-label-has-associated-control -->
              <label class="small">Tasks</label>
              <div style="display:flex; flex-direction:column; gap:8px; margin-top:8px; overflow-y: scroll; max-height: 200px;">
                {#each currentCard.todos as t, idx}
                  <label class="todo-item" class:done={t.done}>
                    <!-- visually-hidden native checkbox -->
                    <input
                      class="native-checkbox"
                      type="checkbox"
                      checked={t.done}
                      on:change={(e) => {
                        currentCard.todos[idx].done = e.target.checked;
                        cards = cards;
                        persist();
                      }} />

                    <!-- custom checkbox -->
                    <span class="custom-check" aria-hidden="true">
                      <svg viewBox="0 0 24 24" width="16" height="16" fill="none">
                        <path class="check-path" d="M4 12l4 4 12-12" stroke="white" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round" />
                      </svg>
                    </span>

                    <input type="text" class="todo-input" bind:value={t.text} placeholder="Task" on:input={() => persist()} />

                    <button class="btn btn-ghost" on:click={() => removeTodo(currentCard, idx)}>Remove</button>
                  </label>
                {/each}
              </div>
              <div style="margin-top:8px;">
                <button class="btn btn-ghost" on:click={() => addTodo(currentCard)}>Add task</button>
              </div>
            {/if}
          </div>

          <div style="display:flex; gap:8px; margin-top:12px; justify-content:flex-end;">
            <button class="btn btn-ghost" on:click={closeOverlay}>Close</button>
          </div>
        {/if}
      </div>
    </div>
  {/key}
{/if}

<style>
  .small { font-size:0.9rem; color:#6b7280; }

  .card-grid {
    display:grid;
    grid-template-columns: repeat(2, 1fr);
    gap:12px;
    margin-top:8px;
  }
  .cat-card {
    background: linear-gradient(12deg, #9fc5ff, #fbfdff);
    border-radius:12px;
    padding:14px;
    display:flex;
    flex-direction:column;
    gap:8px;
    align-items:flex-start;
    border:1px solid rgba(15,23,42,0.04);
    cursor:pointer;
    transition: transform .3s ease, box-shadow .3s ease;
    align-items: center;
  }
  .cat-card:hover { transform: translateY(-4px); box-shadow: 0 10px 24px rgba(16,24,40,0.06); }
  .cat-icon { font-size:28px; }
  .cat-label { font-weight:700; }
  .cat-sub { color:var(--muted); font-size:0.9rem; }

  .overlay {
    position:fixed; inset:0; display:flex; align-items:center; justify-content:center; background:rgba(0,0,0,0.36); z-index:120;
  }
  .modal {
    width:90%; max-width:720px; background:white; padding:18px; border-radius:12px; box-shadow:0 12px 40px rgba(2,6,23,0.2);
  }

  .card-top-badge { position: absolute; right: 10px; top: 8px; }
  .tile-check {
   display:inline-block;
    background: rgba(255,255,255,0.95);
    color: #16a34a;
    border-radius: 8px;
    padding: 4px 6px;
    font-weight:700;
    box-shadow: 0 6px 18px rgba(16,24,40,0.06);
  }
  .cat-card { position: relative; }
  .todo-item {
    display:flex;
    align-items:center;
    gap:10px;
    padding:8px;
    border-radius:8px;
    background: rgba(255,255,255,0.9);
    border:1px solid rgba(15,23,42,0.04);
  }
  .todo-item .native-checkbox {
    position:absolute;
    opacity:0;
    width:1px;
    height:1px;
    pointer-events:all;
  }
  .custom-check {
    width:22px;
    height:22px;
    border-radius:6px;
    background: linear-gradient(180deg,#f1f5f9,#ffffff);
    border:1px solid #d1d5db;
    display:inline-flex;
    align-items:center;
    justify-content:center;
    transition: transform .12s ease, box-shadow .12s ease, background .12s ease;
    flex:0 0 auto;
  }
  .todo-item.done .custom-check {
    background: linear-gradient(90deg,#6aa6ff,#7ee3ff);
    box-shadow: 0 8px 20px rgba(60,120,220,0.12);
    transform: translateY(-2px);
  }
  .custom-check svg { display:block; opacity:0; transform:scale(0.6); transition: opacity .18s ease, transform .18s ease; }
  .todo-item.done .custom-check svg { opacity:1; transform:scale(1); }

  .todo-input {
    flex:1;
    padding:8px;
    border:none;
    outline:none;
    background:transparent;
    font-size:0.95rem;
    color: #0f172a;
  }
  .todo-item.done .todo-input {
    text-decoration: line-through;
    opacity: 0.72;
  }

  /* ensure remove button is compact */
  .todo-item .btn-ghost { padding:6px 8px; }

  /* responsive */
  @media (max-width:720px) {
    .card-grid { grid-template-columns: 1fr; }
  }
</style>
