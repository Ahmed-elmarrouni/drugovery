# Lab 2: Cloud CI/CD Pipeline with GitLab CI

### Step 1: Connecting GitLab to GitHub

The goal of this lab was to migrate the local Jenkins pipeline to a cloud-based environment using GitLab CI. Since my code is hosted on GitHub, I first needed to connect the two platforms.

I generated a **Personal Access Token (PAT)** on GitHub with `repo` and `admin:repo_hook` scopes. Then, I used the "Import Project" feature in GitLab to mirror my `drugovery` repository.

### Step 2: Configuring Secrets (CI/CD Variables)

Unlike Jenkins, where I used the Credentials provider, GitLab uses **CI/CD Variables**. I added my Docker Hub credentials so the pipeline could push images without exposing my password.

I created two variables in **Settings \> CI/CD \> Variables**:

- `DOCKER_USER`: My Docker Hub username (`ahmedelmrn`).
- `DOCKER_PWD`: My Docker Hub Personal Access Token.

_Note: I ensured that both variables were **Masked** to protect them in the logs, and I unchecked **"Protected"** so they could be used on my `DevOps` branch._

### Step 3: Creating the `.gitlab-ci.yml`

I created a `.gitlab-ci.yml` file in the root of the project. Since GitLab runs on shared cloud runners, I used **Docker-in-Docker (dind)** to allow the runner to execute Docker commands inside its own container.

The pipeline is divided into two main jobs:

#### Job 1: `test_backend` (Stage: test)

This job replicates the testing phase from Lab 1:

- **Environment:** Uses the `docker:latest` image with `dind` service.
- **Action:** Installs `docker-cli-compose`, builds the project using the CI-specific compose files, and runs the 5 Python backend tests using `pytest`.
- **Cleanup:** Automatically runs `docker compose down -v` to ensure the runner stays clean.

#### Job 2: `push_to_dockerhub` (Stage: push)

This job handles the distribution:

- **Constraint:** Set to run **only** on the `DevOps` branch.
- **Action:** Logs into Docker Hub using the variables configured in Step 2.
- **Action:** Tags the local images (`backend`, `frontend`, `celery-worker`) with my Docker Hub namespace and pushes them to the public registry.

### Step 4: Verification

Upon committing the configuration, GitLab detected the file and launched the pipeline immediately.

I verified the success by checking the **Pipelines** dashboard. The green "passed" status confirmed that the cloud runner successfully built the images, passed all tests, and updated the repositories on Docker Hub.

### Step 5: Dual-Push Automation (Bonus)

To avoid manual syncing between GitHub and GitLab, I configured my local MacBook to push to both platforms simultaneously.

I modified my local `.git/config` by adding a second push URL to the `origin` remote. I also generated a GitLab Access Token to authorize my terminal.

```bash
# My local configuration for dual-sync
git remote set-url --add --push origin https://github.com/Ahmed-elmarrouni/drugovery.git
git remote set-url --add --push origin https://gitlab.com/ahmedelmarrouni1/drugovery.git
```

Now, every time I run `git push`, the code is sent to both GitHub (triggering Jenkins locally) and GitLab (triggering the cloud pipeline) at the same time.

---

### Summary of Pipeline Jobs

| Job Name                | Stage  | Description                                                           |
| :---------------------- | :----- | :-------------------------------------------------------------------- |
| **`test_backend`**      | `test` | Builds containers and runs Pytest in a `dind` environment.            |
| **`push_to_dockerhub`** | `push` | Tags and pushes 3 Docker images to the registry using masked secrets. |
