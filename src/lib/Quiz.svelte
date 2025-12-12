<script>
  import confetti from "canvas-confetti";

  export let questions = [];
  export let title = "Quiz";
  export let onBack;

  let index = 0;
  let score = 0;
  let finished = false;
  let selected = null;
  let timeLeft = 0;
  let timer;

  $: current = questions[index];
  $: correctIndex = current ? parseInt(current.correct_answer) - 1 : -1;

  function startTimer() {
    timeLeft = current.time_limit || 20;
    clearInterval(timer);
    timer = setInterval(() => {
      timeLeft--;
      if (timeLeft <= 0) select(-1);
    }, 1000);
  }

  $: if (current && !finished && selected === null) startTimer();

  function select(i) {
    if (selected !== null) return;
    clearInterval(timer);
    selected = i;

    // Check answer (1-based index in YAML)
    if (i === correctIndex) score++;

    setTimeout(next, 1500);
  }

  let copied = false;

  async function shareResult() {
    const text = `I've scored ${score}/${questions.length} in the '${title}' in Zarrhoot!`;
    const url = window.location.href;
    const shareData = {
      title: "ZarrHoot Quiz Result",
      text: text,
      url: url,
    };

    if (navigator.share) {
      try {
        await navigator.share(shareData);
      } catch (err) {
        // User cancelled or share failed, fallback to clipboard if not a cancellation
        if (err.name !== "AbortError") {
          copyToClipboard(`${text} Try it at: ${url}`);
        }
      }
    } else {
      copyToClipboard(`${text} Try it at: ${url}`);
    }
  }

  function copyToClipboard(content) {
    navigator.clipboard.writeText(content).then(() => {
      copied = true;
      setTimeout(() => (copied = false), 2000);
    });
  }

  function next() {
    selected = null;
    if (index < questions.length - 1) {
      index++;
    } else {
      finished = true;
      if (score === questions.length) confetti();
    }
  }
</script>

<div class="card">
  {#if !finished}
    <div
      style="display: flex; justify-content: space-between; margin-bottom: 20px; color: #666;"
    >
      <button on:click={onBack} style="background:none; color:inherit;"
        >Exit</button
      >
      <span>{index + 1} of {questions.length}</span>
    </div>

    <div class="timer-bar">
      <div
        class="timer-fill"
        style="width: {(timeLeft / (current.time_limit || 20)) * 100}%"
      ></div>
    </div>

    <h2 class="question-text">{current.question}</h2>

    <div class="answers-grid">
      {#each current.answers as answer, i}
        <button
          class="answer-btn
            {selected !== null && i === correctIndex ? 'correct' : ''}
            {selected !== null && selected === i && i !== correctIndex
            ? 'wrong'
            : ''}
            {selected !== null && i !== correctIndex && i !== selected
            ? 'dimmed'
            : ''}
          "
          on:click={() => select(i)}
          disabled={selected !== null}
        >
          <div class="shape"></div>
          {answer}
        </button>
      {/each}
    </div>
  {:else}
    <h1 class="title">{title}</h1>
    <p class="subtitle">Quiz Finished!</p>
    <p class="subtitle">You scored {score} out of {questions.length}</p>

    <div
      style="display: flex; gap: 10px; justify-content: center; margin-top: 20px;"
    >
      <button class="btn-control" on:click={onBack}>Back to Menu</button>
      <button
        class="btn-control"
        style="background-color: #4CAF50; display: flex; align-items: center; gap: 8px;"
        on:click={shareResult}
      >
        {#if !copied}
          <svg
            xmlns="http://www.w3.org/2000/svg"
            width="16"
            height="16"
            viewBox="0 0 24 24"
            fill="none"
            stroke="currentColor"
            stroke-width="2"
            stroke-linecap="round"
            stroke-linejoin="round"
          >
            <circle cx="18" cy="5" r="3"></circle>
            <circle cx="6" cy="12" r="3"></circle>
            <circle cx="18" cy="19" r="3"></circle>
            <line x1="8.59" y1="13.51" x2="15.42" y2="17.49"></line>
            <line x1="15.41" y1="6.51" x2="8.59" y2="10.49"></line>
          </svg>
        {/if}
        {copied ? "Copied!" : "Share Result"}
      </button>
    </div>
  {/if}
</div>
