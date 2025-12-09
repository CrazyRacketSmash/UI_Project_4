<script>
  import { createEventDispatcher, onMount } from 'svelte';
  const dispatch = createEventDispatcher();

  const questions = [
    { id: 1, question: 'Where is this sadness coming from?', placeholder: 'What triggered these feelings?' },
    { id: 2, question: 'How long have you been feeling this way?', placeholder: 'Hours, days, weeks...' },
    { id: 3, question: 'What do you need right now?', placeholder: 'Rest, support, distraction...' },
    { id: 4, question: 'Have you talked to someone about this?', placeholder: 'Who could help?' },
    { id: 5, question: 'What has helped you before?', placeholder: 'Music, nature, rest...' },
    { id: 6, question: 'What small thing can you do today?', placeholder: 'One tiny act of self-care...' }
  ];

  let responses = {};
  let currentIndex = 0;
  let fillPercentage = 0;
  let suggestions = [];

  onMount(() => {
    //When the Sad mood is selected, reset these
    responses = {};
    currentIndex = 0;
    fillPercentage = 0;

    // Clear this localStorage key only when entering the healing view
    localStorage.removeItem('sad_healing_responses');
  });

  function generateSuggestions() {
    const allText = Object.values(responses).join(' ').toLowerCase();
    const suggestionSet = new Set();

    // keyword-based suggestion mapping
    if (allText.includes('alone') || allText.includes('isolated') || allText.includes('nobody')) {
      suggestionSet.add({ emoji: '💬', title: 'Reach out', desc: 'Send a message to someone you trust. Even a small "hi" counts.' });
    }
    if (allText.includes('tired') || allText.includes('exhausted') || allText.includes('sleep')) {
      suggestionSet.add({ emoji: '😴', title: 'Rest', desc: 'Give yourself permission to rest. Your body and mind need it.' });
    }
    if (allText.includes('overwhelm') || allText.includes('stress') || allText.includes('too much')) {
      suggestionSet.add({ emoji: '🧘', title: 'Breathe', desc: 'Take 5 slow, deep breaths. Pause everything else for a moment.' });
    }
    if (allText.includes('nature') || allText.includes('outside') || allText.includes('walk')) {
      suggestionSet.add({ emoji: '🌿', title: 'Go outside', desc: 'Spend 10 minutes in nature. Even a window view helps.' });
    }
    if (allText.includes('music') || allText.includes('song')) {
      suggestionSet.add({ emoji: '🎵', title: 'Listen to music', desc: 'Play songs that match or soothe your emotions.' });
    }
    if (allText.includes('help') || allText.includes('therapy') || allText.includes('talk')) {
      suggestionSet.add({ emoji: '👂', title: 'Talk to someone', desc: 'Consider speaking with a therapist or counselor.' });
    }
    if (allText.includes('creative') || allText.includes('art') || allText.includes('write')) {
      suggestionSet.add({ emoji: '🎨', title: 'Create', desc: 'Express yourself through art, writing, or music.' });
    }
    if (allText.includes('self-care') || allText.includes('bath') || allText.includes('tea')) {
      suggestionSet.add({ emoji: '🛀', title: 'Self-care ritual', desc: 'Do something gentle and nurturing for yourself.' });
    }

    // always add these core suggestions
    if (suggestionSet.size === 0) {
      suggestionSet.add({ emoji: '❤️', title: 'Be kind to yourself', desc: 'You deserve compassion, especially from yourself.' });
      suggestionSet.add({ emoji: '🌅', title: 'Notice one good thing', desc: 'Find one small thing today that brings a little warmth.' });
    }

    suggestions = Array.from(suggestionSet);
  }

  function answerQuestion() {
    const q = questions[currentIndex];
    if (!responses[q.id]) {
      responses[q.id] = '';
    }
    
    if (currentIndex < questions.length - 1) {
      // Move to next question
      currentIndex++;
      fillPercentage = (currentIndex / questions.length) * 100;
      persist();
    } else {
      // When last question answered - fill to 100% and generate suggestions
      currentIndex++;
      fillPercentage = 100;
      generateSuggestions();
      persist();
    }
  }

  function persist() {
    try {
      localStorage.setItem('sad_healing_responses', JSON.stringify(responses));
    } catch(e){}
  }

  function finish() {
    dispatch('back');
  }

  $: if (responses[questions[currentIndex]?.id]) {
    // auto save
    persist();
  }
</script>

<div class="healing-container">
  <h3 style="margin:0 0 8px 0">Emotional Reflection</h3>
  <p class="small">Let's explore what you're feeling. Your answers help you understand and heal.</p>

  <div class="beaker-section">
    <!-- Beaker visualization -->
    <div class="beaker-wrapper">
      <div class="beaker">
        <!-- Rising bubbles inside beaker -->
        <div class="beaker-bubbles" style="opacity: {Math.min(fillPercentage / 20, 1)}; clip-path: inset({100 - fillPercentage}% 0 0 0);">
           <div class="bubble bubble-1"></div>
           <div class="bubble bubble-2"></div>
           <div class="bubble bubble-3"></div>
           <div class="bubble bubble-4"></div>
           <div class="bubble bubble-5"></div>
           <div class="bubble bubble-6"></div>
         </div>

        <div class="beaker-liquid" style="height: {fillPercentage}%"></div>
        <div class="beaker-label">{Math.round(fillPercentage)}%</div>
      </div>
    </div>

    <!-- Question area -->
    <div class="question-area">
      {#if currentIndex < questions.length}
        <div class="question-card">
          <div class="progress-bar">
            <div class="progress-fill" style="width: {(currentIndex / questions.length) * 100}%"></div>
          </div>
          <h4 style="margin:0 0 12px 0; font-size:1.15rem; color:#064e6b;">
            {questions[currentIndex].question}
          </h4>
          <textarea
            bind:value={responses[questions[currentIndex].id]}
            placeholder={questions[currentIndex].placeholder}
            rows="4"
            style="width:100%; padding:12px; border-radius:8px; border:1px solid rgba(14,165,233,0.12); font-size:1rem; resize:none;"
          ></textarea>
          <div style="display:flex; gap:8px; margin-top:12px;">
            <button class="btn btn-primary" on:click={answerQuestion} disabled={!responses[questions[currentIndex].id]?.trim()}>
              {currentIndex === questions.length - 1 ? 'Finish' : 'Next'}
            </button>
            <button class="btn btn-ghost" on:click={() => dispatch('back')}>Back</button>
          </div>
        </div>
      {:else}
        <div class="question-card" style="text-align:left;">
          <p class="small" style="text-align:center; margin-bottom:16px;">You've taken an important step in understanding your emotions. Here's something you can try:</p>

          <!-- Personalized suggestions -->
          <div style="display:flex; flex-direction:column; gap:10px; margin-bottom:16px;">
            {#each suggestions as suggestion}
              <div style="padding:12px; border-radius:8px; background:rgba(14,165,233,0.05); border-left:4px solid #06d6a0;">
                <div style="font-weight:700; margin-bottom:4px; display:flex; gap:8px; align-items:center;">
                  <span>{suggestion.emoji}</span>
                  <span style="color:#064e6b;">{suggestion.title}</span>
                </div>
                <p style="margin:0; font-size:0.9rem; color:#6b7280;">{suggestion.desc}</p>
              </div>
            {/each}
          </div>

          <h4 class="small" style="color:#999; text-align:center; margin-bottom:12px;">Remember, healing takes time...Be patient with yourself!</h4>
          <button class="btn btn-primary" on:click={finish} style="width:100%;">Close</button>
        </div>
      {/if}
    </div>
  </div>
</div>

<style>
  .healing-container {
    display: flex;
    flex-direction: column;
    gap: 8px;
  }

  .small { font-size:0.9rem; color:#6b7280; }

  .beaker-section {
    display: flex;
    gap: 24px;
    align-items: flex-start;
  }

  .beaker-wrapper {
    flex: 0 0 200px;
    display: flex;
    justify-content: center;
    align-items: flex-start;
  }

  /* Beaker */
  .beaker {
    position: relative;
    width: 120px;
    height: 280px;
    border: 3px solid #0ea5e9;
    border-radius: 0 0 24px 24px;
    background: linear-gradient(180deg, rgba(14,165,233,0.05), rgba(14,165,233,0.02));
    overflow: hidden;
    box-shadow: 0 16px 48px rgba(14,165,233,0.15);
  }

  /* Liquid inside beaker */
  .beaker-liquid {
    position: absolute;
    bottom: 0;
    left: 0;
    right: 0;
    background: linear-gradient(180deg, #06d6a0, #06b6d4, #0ea5e9);
    transition: height 0.8s cubic-bezier(0.34, 1.56, 0.64, 1);
    opacity: 0.85;
    box-shadow: inset 0 -4px 12px rgba(14,165,233,0.2);
  }

  /* Percentage label */
  .beaker-label {
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    font-weight: 700;
    font-size: 1.8rem;
    color: #064e6b;
    text-shadow: 0 2px 8px rgba(255,255,255,0.6);
    z-index: 10;
    pointer-events: none;
  }

  /* Beaker rim */
  .beaker::before {
    content: "";
    position: absolute;
    top: -8px;
    left: 50%;
    transform: translateX(-50%);
    width: 140px;
    height: 12px;
    background: linear-gradient(180deg, #0ea5e9, #06b6d4);
    border-radius: 50% 50% 0 0;
    z-index: 2;
  }

  .question-area {
    flex: 1;
    min-width: 320px;
  }

  .question-card {
    background: linear-gradient(180deg, #ffffff, #f0f9ff);
    padding: 20px;
    border-radius: 12px;
    border: 1px solid rgba(14,165,233,0.12);
    box-shadow: 0 8px 24px rgba(14,165,233,0.08);
  }

  .progress-bar {
    width: 100%;
    height: 6px;
    background: rgba(14,165,233,0.1);
    border-radius: 3px;
    margin-bottom: 16px;
    overflow: hidden;
  }

  .progress-fill {
    height: 100%;
    background: linear-gradient(90deg, #06d6a0, #06b6d4, #0ea5e9);
    transition: width 0.5s ease;
  }

  textarea {
    font-family: inherit;
  }

  .btn {
    padding: 10px 16px;
    border-radius: 8px;
    border: none;
    cursor: pointer;
    font-weight: 600;
    transition: transform .12s ease;
  }

  .btn-primary {
    background: linear-gradient(90deg, #06d6a0, #0ea5e9);
    color: white;
  }

  .btn-primary:hover:not(:disabled) {
    transform: translateY(-2px);
    box-shadow: 0 10px 24px rgba(14,165,233,0.2);
  }

  .btn-primary:disabled {
    opacity: 0.5;
    cursor: not-allowed;
  }

  /* Beaker bubbles */
  .beaker-bubbles {
    position: relative;
    inset: 0;
    height: 100%;
    pointer-events: none;
    transition: opacity 0.6s ease;
  }

  .bubble {
    position: relative;
    bottom: 10px;
    border-radius: 50%;
    background: radial-gradient(circle at 32% 32%, #E91E63, #607D8B, #FFEB3B);
    box-shadow: inset -2px -2px 6px rgba(14,165,233,0.25), inset 2px 2px 6px rgba(255,255,255,0.7), 0 8px 16px rgba(79,156,232,0.2);
    opacity: 0;
  }

  .bubble-1 { width:18px; height:18px; left:15%; animation: bubble-rise-liquid 3s ease-out infinite; }
  .bubble-2 { width:22px; height:22px; left:35%; animation: bubble-rise-liquid 3.4s ease-out 0.4s infinite; }
  .bubble-3 { width:16px; height:16px; left:55%; animation: bubble-rise-liquid 3.2s ease-out 0.8s infinite; }
  .bubble-4 { width:20px; height:20px; left:72%; animation: bubble-rise-liquid 3.6s ease-out 0.2s infinite; }
  .bubble-5 { width:14px; height:14px; left:25%; animation: bubble-rise-liquid 2.8s ease-out 1.2s infinite; }
  .bubble-6 { width:17px; height:17px; left:65%; animation: bubble-rise-liquid 3.3s ease-out 0.6s infinite; }

  @keyframes bubble-rise-liquid {
     0% {
       transform: translateY(0) scale(1);
       opacity: 0;
     }
     10% {
      opacity: 0.8;
     }
     85% {
      opacity: 0.5;
     }
     100% {
      transform: translateY(-100%) scale(0.85);
       opacity: 0;
     }
   }

  @media (max-width: 720px) {
    .beaker-section {
      flex-direction: column;
      align-items: center;
    }

    .question-area {
      width: 100%;
    }
  }
</style>
