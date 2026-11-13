# GitHub Actions workflows (submission version)

All four workflows run when a commit is **pushed** to the `main` or `master` branch. A local `git commit` without `git push` does not trigger GitHub Actions.

| Workflow | Main stages |
|---|---|
| `llm-multiroute-ci.yml` | Ruff (E4,E7,E9,F821) → pytest → Docker Build → Trivy → optional Docker Hub push |
| `llm-frontend-python-ci.yml` | Ruff (E4,E7,E9,F821) → Docker Build → Trivy → optional Docker Hub push |
| `promptfoo-tests-ci.yml` | Node 22 → Docker Compose backend → 4 Promptfoo evaluations |
| `deepeval-tests-ci.yml` | Python 3.12 → Docker Compose backend → Ollama Cloud judge check → 4 DeepEval suites |

Both `pull_request` triggers remain restricted to the relevant folders to avoid unrelated PR runs. Manual runs remain available via `workflow_dispatch`.

Required repository secret: `OLLAMA_API_KEY` (backend and LLM-as-judge use Ollama Cloud).
Optional repository secret: `OLLAMA_BASE_URL` (defaults to `https://ollama.com` in Compose).
Optional repository variable: `DOCKERHUB_USERNAME` (your own Docker Hub user).
Optional repository secret: `DOCKERHUB_TOKEN` (required if `DOCKERHUB_USERNAME` is configured and images should be pushed).
`OPENAI_API_KEY` is optional for the current Ollama-based judge but may be needed for OpenAI judge configuration.

Publishing images is skipped unless both Docker Hub settings are configured. Build and Trivy scan still run.

The original `.trivyignore` contains multiple historical CVE suppressions. They were not silently removed. Review and justify them prior to using the results as security evidence.

For GitHub upload and prerequisites, read the root `README.md`.
