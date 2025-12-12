<script>
  import { onMount } from "svelte";
  import yaml from "js-yaml";
  import Quiz from "./lib/Quiz.svelte";

  const QUIZ_FILES = [
    "quiz.yaml",
    "ngff_intro.yaml",
    "rfc1_process.yaml",
    "rfc2_zarr_v3.yaml",
  ];

  let quizzes = [];
  let activeQuiz = null;
  let loading = true;

  onMount(async () => {
    try {
      const loadedQuizzes = await Promise.all(
        QUIZ_FILES.map(async (file) => {
          const response = await fetch(import.meta.env.BASE_URL + file);
          const text = await response.text();
          const data = yaml.load(text);
          return { ...data, id: file };
        }),
      );
      quizzes = loadedQuizzes;

      // Check URL for quiz param
      const params = new URLSearchParams(window.location.search);
      const quizId = params.get("quiz");
      if (quizId) {
        const found = quizzes.find((q) => q.id === quizId);
        if (found) activeQuiz = found;
      }
    } catch (e) {
      console.error(e);
    } finally {
      loading = false;
    }
  });

  function selectQuiz(quiz) {
    activeQuiz = quiz;
    const url = new URL(window.location.href);
    url.searchParams.set("quiz", quiz.id);
    window.history.pushState({}, "", url);
  }

  function clearQuiz() {
    activeQuiz = null;
    const url = new URL(window.location.href);
    url.searchParams.delete("quiz");
    window.history.pushState({}, "", url);
  }
</script>

<main class="container">
  {#if activeQuiz}
    <Quiz
      questions={activeQuiz.questions}
      title={activeQuiz.title}
      onBack={clearQuiz}
    />
  {:else}
    <div class="card">
      <h1 class="title">ZarrHoot</h1>
      <p class="subtitle">Select a quiz to start</p>

      {#if loading}
        <p>Loading...</p>
      {:else}
        <div class="quiz-list">
          {#each quizzes as quiz}
            <button class="quiz-btn" on:click={() => selectQuiz(quiz)}>
              <strong>{quiz.title}</strong>
              <div style="font-size: 0.9rem; color: #666; margin-top: 5px;">
                {quiz.description}
              </div>
            </button>
          {/each}
        </div>
      {/if}
    </div>
  {/if}
</main>
