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
    <h1 class="title">Quiz Finished!</h1>
    <p class="subtitle">You scored {score} out of {questions.length}</p>

    <button class="btn-control" on:click={onBack}>Back to Menu</button>
  {/if}
</div>
