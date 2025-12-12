# ZarrHoot

A minimal, open-source quiz platform for the OME-Zarr community, built with Svelte and Vite.

## 🚀 Getting Started

1.  **Install dependencies:**
    ```bash
    npm install
    ```

2.  **Run the development server:**
    ```bash
    npm run dev
    ```

3.  Open your browser at `http://localhost:5173`.

## 📝 Adding New Quizzes

We welcome contributions! If you have a quiz idea, please submit a Pull Request.

1.  Create a new `.yaml` file in the `public/` directory (e.g., `my_new_quiz.yaml`).
2.  Follow this format:

    ```yaml
    title: "My New Quiz"
    description: "A short description of what this quiz covers."
    questions:
      - question: "What is the answer to life, the universe, and everything?"
        answers:
          - "42"
          - "24"
          - "0"
          - "Infinity"
        time_limit: 20
        correct_answer: "1" # 1-based index of the correct answer
    ```

3.  Register your new quiz in `src/App.svelte` by adding the filename to the `QUIZ_FILES` array:

    ```javascript
    const QUIZ_FILES = ['quiz.yaml', 'ngff_intro.yaml', 'my_new_quiz.yaml'];
    ```

## ⚖️ License

*   **Source Code**: The application code is licensed under the [MIT License](LICENSE). Copyright © 2025 German BioImaging.
*   **Quiz Content**: All quiz questions and content are released under [CC0 1.0 Universal (Public Domain Dedication)](https://creativecommons.org/publicdomain/zero/1.0/). You are free to copy, modify, and distribute the quiz content without asking for permission.
