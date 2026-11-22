# AI381 Lab 3 — Windows / PyCharm steps

Use Git Bash or WSL2 Ubuntu terminal for shell commands. Ensure Docker Desktop is running.

## Part 0: baseline
```bash
python -m venv .venv
source .venv/Scripts/activate  # Git Bash on Windows; WSL: source .venv/bin/activate
python -m pip install -r requirements-dev.txt
python -m src.train.train
pytest -q
```

## Parts 1–4: MLflow, candidates, promotion
```bash
docker compose -f docker-compose.mlflow.yml up -d --wait
export MLFLOW_TRACKING_URI=http://localhost:5000
python -m src.train.train_mlflow
# Open http://localhost:5000 and compare three runs
python scripts/promote.py
cat artifacts/champion.json
```
Complete MODEL_SELECTION.md using actual MLflow results before committing.

## Part 5: GitHub Actions
Create your own GitHub repository, commit this project on a lab3 branch, push it and open a PR. Confirm train-and-gate is green. Follow instructor steps to preserve champion artifacts on main. A fresh CI runner does not preserve your local MLflow server.

## Parts 6–7: local deployment and lineage
```bash
git checkout main && git pull
docker build --build-arg GIT_SHA=$(git rev-parse HEAD) -t churn-api:local .
docker tag churn-api:local churn-api:$(git rev-parse --short HEAD)
docker compose -f docker-compose.deploy.yml up -d
bash scripts/smoke_test.sh http://localhost:8000
curl -s http://localhost:8000/health
git rev-parse --short HEAD
docker images churn-api
```

## Part 8: negative gate
On a new branch, set CANDIDATES to only weak LogisticRegression(C=0.00001, max_iter=1000). Open a PR and capture BLOCKED from the failed GitHub Actions check. Do not merge this branch.

## Video
Record 5–8 minutes with webcam, voice, identity/profile and checkpoints 0–6. Submit video and repo.txt on Teams.

Note: actual training, Docker, GitHub, and screenshots must be run and verified on your own machine.
