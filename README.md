# CI/CD Pipeline & Cloud Deployment Automation

A fully automated CI/CD pipeline that builds, tests, containerizes, and deploys a Python web application to AWS EC2 — triggered automatically on every `git push`.

## What it does

This project eliminates manual deployment steps by automating the entire journey from code commit to a live server:

1. **Test** — Every push automatically runs `pytest` to catch broken code before it goes anywhere
2. **Build** — The app is containerized with Docker, ensuring it runs identically on any machine
3. **Push** — The Docker image is pushed to GitHub Container Registry (GHCR)
4. **Deploy** — GitHub Actions SSHs into an AWS EC2 instance, pulls the latest image, and restarts the container — with zero manual intervention

Separately, an **Ansible playbook** automates provisioning a brand-new EC2 server from scratch (installing Docker, configuring the environment) — so the entire infrastructure setup is repeatable and not dependent on manual server configuration.

## Tech Stack

- **Application**: Python, Flask
- **Testing**: pytest
- **Containerization**: Docker
- **CI/CD**: GitHub Actions
- **Container Registry**: GitHub Container Registry (GHCR)
- **Cloud Hosting**: AWS EC2
- **Infrastructure Automation**: Ansible

## Pipeline Architecture

```
git push
    |
    v
GitHub Actions triggered
    - Run tests (pytest)
    - Build Docker image
    - Push image to GHCR
    |
    v
SSH into EC2 instance
    - Pull latest image, restart container
    |
    v
App live on EC2 (port 80)
```

Ansible playbook (run separately) provisions a fresh EC2 instance with Docker pre-installed and configured, making server setup fully repeatable.

## Project Structure

```
app.py                          Flask application
test_app.py                     Automated tests
requirements.txt                Python dependencies
Dockerfile                      Container definition
inventory.ini                   Ansible inventory
playbook.yml                    Ansible server provisioning
.github/workflows/deploy.yml    CI/CD pipeline definition
```
## Key Engineering Decisions

- Switched from Docker Hub to GitHub Container Registry after diagnosing a persistent authentication issue in the CI environment — GHCR's built-in token authentication removed an entire class of credential-related failures.
- Used environment variables instead of inline secret interpolation in the GitHub Actions workflow to avoid shell-injection risks when passing credentials.
- Separated server provisioning (Ansible) from app deployment (GitHub Actions) so infrastructure setup and application releases can evolve independently.

## Running Locally
pip install -r requirements.txt
pytest
python app.py

## Author

Kuluru Sudharshan Reddy
