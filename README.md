# LLMSecOps — LLMApp09

NUS-ISS course exercise repository demonstrating practical LLM operations: API serving, multi-model routing, safety checks, automated evaluations, Docker packaging, and GitHub Actions.

## Projects

| Folder | Purpose |
| --- | --- |
| `llm-python/` | Basic Python LLM API |
| `llm-multiroute/` | Multi-model routing backend, guardrails and metrics |
| `llm-frontend-python/` | Python frontend |
| `promptfoo-tests/` | Four Promptfoo API evaluations |
| `deepeval-tests/` | Four DeepEval LLM-judge suites |
| `.github/workflows/` | Four CI/CD GitHub Actions workflows |

Additional notes: `langfuse.md`, `docker_readme.md`, `workflow_readme.md`.

## Quick start

1. Set the API credentials locally without committing them:

   ```bash
   cp llm-multiroute/.env.example .env
   # Edit .env with your own OLLAMA_API_KEY and model settings
   ```

2. Start the backend and frontend:

   ```bash
   docker compose up --build -d
   ```

3. Open `http://localhost:5000`. The backend is available at `http://localhost:8080`.

4. Run unit tests:

   ```bash
   cd llm-multiroute
   python -m pip install -r requirements.txt
   python -m pytest tests/ -v
   ```

For evaluation instructions, see `promptfoo-tests/PROMPTFOO_INSTRUCTIONS.md` and `deepeval-tests/DEEPEVAL_INSTRUCTIONS.md`.

## GitHub Actions (course submission)

Four workflows run when you **push** a commit to `main` or `master` (a local `git commit` alone does not start a remote GitHub Actions run). Each also supports manual **Run workflow**; pull requests retain component-specific path filters.

- `LLM Multiroute CI`: Ruff syntax/undefined-name check → unit tests → Docker build → Trivy high/critical scan → optional Docker Hub push.
- `LLM Frontend Python CI`: Ruff syntax/undefined-name check → Docker build → Trivy scan → optional Docker Hub push.
- `PromptFoo Tests`: builds the backend and runs four LLM API evaluations.
- `DeepEval Tests`: builds the backend and runs four LLM-judge evaluations.

**Repository settings**: In **Settings → Secrets and variables → Actions**, configure:

- **Secrets**: `OLLAMA_API_KEY` (required for Cloud model and judge tests). `DOCKERHUB_TOKEN` only if publishing Docker Hub images. `OPENAI_API_KEY` optional for OpenAI-based evaluations; present workflow defaults to an Ollama judge.
- **Variables**: `DOCKERHUB_USERNAME` (your own Docker Hub account; optional if you only need CI build/scan).
- **Secret (optional)**: `OLLAMA_BASE_URL` if you must override the default `https://ollama.com`.

The backend/Promptfoo/DeepEval tests call external LLM services and may incur charges or fail if credentials, model access, or quotas are unavailable. The current CI judge is `glm-5.2` on Ollama Cloud. Check that the specified cloud models are available to your account.

GitHub **Actions → select workflow → open latest commit run** shows whether each job passes. Keep your commit SHA and run URLs (and optionally screenshots) as evidence of the pipeline execution.

### First-time upload

Create a new empty GitHub repository named `llmapp09` (do not pre-create a README), then, **inside this folder**:

```bash
git init
git branch -M main
git add .
git status
git commit -m "Submit LLMSecOps project with GitHub Actions"
git remote add origin https://github.com/YOUR_USERNAME/llmapp09.git
git push -u origin main
```

**Important**: Keep `.github/` and `docker-compose.yml` at the repository root, not inside an extra `llmapp09/` directory on GitHub. Do not commit `.env` or real API keys.

## Security note

`.trivyignore` was retained from the original project and contains a number of CVE exceptions. Review each exception and document the reason and expiry before treating the scan as comprehensive. A green job does not mean there are no vulnerabilities.
