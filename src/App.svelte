<script>
  import { onMount } from "svelte";
  import yaml from "js-yaml";
  import Quiz from "./lib/Quiz.svelte";

  const QUIZ_FILES = ["quiz.yaml", "ngff_intro.yaml"];

  let quizzes = [];
  let activeQuiz = null;
  let loading = true;

  onMount(async () => {
    try {
      const loadedQuizzes = await Promise.all(
        QUIZ_FILES.map(async (file) => {
          const response = await fetch(import.meta.env.BASE_URL + file);
          const text = await response.text();
          return yaml.load(text);
        }),
      );
      quizzes = loadedQuizzes;
    } catch (e) {
      console.error(e);
    } finally {
      loading = false;
    }
  });
</script>

<main class="container">
  {#if activeQuiz}
    <Quiz
      questions={activeQuiz.questions}
      title={activeQuiz.title}
      onBack={() => (activeQuiz = null)}
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
            <button class="quiz-btn" on:click={() => (activeQuiz = quiz)}>
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
