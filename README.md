# FIEZEL V6 — Verified Adaptive Learning Build

FIEZEL V6 is a local-first, mobile-first English learning PWA rebuilt around one Master Vocabulary source. It contains a Flashcard engine with browser pronunciation, validated question rendering, adaptive practice, placement test, reading, grammar, progress tracking, and a local contextual FIEZEL tutor. No Gemini/OpenAI/Anthropic API is required.

## Architecture
- `vocabulary-master.json` — single vocabulary source of truth.
- `grammar-bank.json` — grammar questions linked to Master Vocabulary where source data supports it.
- `reading-bank.json` — passage/question bank.
- `level-test-bank.json` — exactly 150 placement questions across multiple skills.
- `app.js` — single application engine; no legacy content engine.
- `validator.js` — offline structural validator.
- `sw.js` — versioned network-first service worker to avoid stale V5 assets.

## Flashcards
Each card supports word, phonetic, meaning, part of speech, example, example translation when verified, browser English pronunciation for the word and example, flip, and self-rating. Ratings update local review scheduling.

## Adaptive practice
Priority is based on due review, recent mistakes, mastery, and unseen material. Vocabulary, grammar, and reading are tracked separately while the Adaptive session chooses across the three domains. Question IDs are blocked from reuse in the active session history.

## Content integrity
A question is not rendered unless it has a valid answer index, unique options, non-empty prompt, and a non-empty correct option. The app never falls back from a failed vocabulary question to a generic grammar question.

The current Master Vocabulary contains source material inherited from the previous project. Items that could not be independently verified are explicitly marked `needs_review`; the build does not silently fabricate translations or examples. The three demonstrated problem words (`advance`, `construct`, `develop`) have been corrected editorially in V6.

## Local run
Serve this directory with any static server, for example `python3 -m http.server 8000`, then open `http://localhost:8000/`.

## GitHub Pages
Upload the contents of this folder to the repository branch/folder used by GitHub Pages. Keep all JSON files at the same relative path as `index.html`. V6 uses versioned asset URLs and a network-first service worker so new deployments are not trapped behind the V5 cache.

## Validation
Run:
`node validator.js .`

The command exits non-zero when structural gates fail. Do not call a build final when the report contains unmet content-quality gates. Structural validation cannot prove semantic truth of every Indonesian translation; those entries remain explicitly marked `needs_review` until editorial verification.

## Adding vocabulary
Add or edit entries only in `vocabulary-master.json`. Do not create another vocabulary database. Required core fields are `id`, `word`, `phonetic`, `partOfSpeech`, `level`, `meaning`, `example`, and `exampleTranslation`; if a field cannot be verified, mark `needs_review` and provide `needsReviewReason`.

## Adding questions
Question objects must have a stable ID, type, skill, question text, unique options, and an `answerIndex` pointing to the correct option. Keep question-type validators separate for grammar, vocabulary, reading, translation, and placement content.

## Audio
FIEZEL uses browser `SpeechSynthesis` for local pronunciation. Voice availability depends on the device/browser. The app never claims audio exists when the browser has no speech synthesis support.
