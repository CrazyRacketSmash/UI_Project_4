<script>
  import { createEventDispatcher } from 'svelte';
  const dispatch = createEventDispatcher();

  export let mood = null;

  let items = [
    { id: 1, text: '', placeholder: 'Put away / finish item 1', checked: false },
    { id: 2, text: '', placeholder: 'Put away / finish item 2', checked: false }
  ];

  let nextId = 3;

  function addItem() {
    items = [...items, { id: nextId++, text: '', placeholder: `Put away / finish item ${nextId-1}`, checked: false }];
  }

  function removeItem(i) {
    items = items.slice(0, i).concat(items.slice(i + 1));
  }

  // Archive selected items
  function archiveSelected() {
    const selected = items.filter(it => it.checked);
    if (!selected.length) return;
    // save into a simple list in localStorage
    try {
      const prev = JSON.parse(localStorage.getItem('tidy_archived') || '[]');
      const toSave = selected.map(s => ({ ...s, archivedAt: Date.now(), mood: mood?.id || 'unknown' }));
      localStorage.setItem('tidy_archived', JSON.stringify(prev.concat(toSave)));
    } catch (e) {}
    // remove archived from current items
    items = items.filter(it => !it.checked);
    // notify parent with archived items
    dispatch('archived', selected);
  }

  // Finish the tidy exercise
  function finish() {
    dispatch('done');
  }
</script>

<div>
  <h3 style="margin:0 0 8px 0">Tidy items</h3>
  <p class="small" style="color:#444">Name small things to put away/clean or finish. Check items and tap "Archive selected" to move them to Archived.</p>

  <div style="margin-top:12px; display:flex; flex-direction:column; gap:10px;">
    {#each items as it, i}
      <label class:checked={it.checked} style="display:flex; align-items:center; gap:12px; padding:10px; border-radius:10px; border:1px solid rgba(15,23,42,0.04); background:var(--card); transition:transform .18s ease, box-shadow .18s ease;">
        <input class="native-checkbox" type="checkbox" bind:checked={it.checked} aria-label="Complete item {i+1}" />
        <span class="check" aria-hidden="true"></span>

        <input
          type="text"
          bind:value={it.text}
          placeholder={it.placeholder}
          style="flex:1; border:none; outline:none; font-size:1rem; background:transparent;"
        />

        <button class="btn btn-ghost" type="button" title="Remove item" on:click={() => removeItem(i)} style="padding:6px 8px;">✕</button>
      </label>
    {/each}
  </div>

  <div style="display:flex; gap:8px; margin-top:12px; align-items:center;">
    <button class="btn btn-ghost" on:click={addItem}>+ Add item</button>
    <button class="btn btn-primary" on:click={archiveSelected} disabled={!items.some(it => it.checked)}>Archive selected</button>
    <div style="flex:1"></div>
    <button class="btn btn-primary" on:click={finish}>Done</button>
  </div>
</div>

<style>
  .small { font-size:0.9rem; color:#6b7280; }
  .native-checkbox {
    position: absolute;
    opacity: 0;
    width: 1px;
    height: 1px;
    pointer-events: all;
  }

  .check {
    width:22px;
    height:22px;
    border-radius:6px;
    background: white;
    border: 1px solid #d1d5db;
    display:inline-block;
    flex: 0 0 auto;
    transition: all .18s ease;
    box-shadow: none;
    position: relative;
  }

  .check::after {
    content: "";
    position: absolute;
    left: 6px;
    top: 2px;
    width: 5px;
    height: 10px;
    border: solid white;
    border-width: 0 2px 2px 0;
    transform: rotate(45deg) scale(0.8);
    opacity: 0;
    transition: opacity .18s ease, transform .18s ease;
  }

  label.checked .check {
    background: linear-gradient(90deg,#6aa6ff,#7ee3ff);
    border-color: transparent;
    transform: translateY(-3px) scale(1.02);
    box-shadow: 0 10px 28px rgba(79,156,232,0.12);
  }
  label.checked .check::after {
    opacity: 1;
    transform: rotate(45deg) scale(1);
  }

  .native-checkbox:focus + .check {
    box-shadow: 0 6px 18px rgba(79,156,232,0.12);
    outline: none;
  }

  input[type="text"]::placeholder { color: #9aa6b2; }

  :global(:root) { --card: #fff; }
</style>
