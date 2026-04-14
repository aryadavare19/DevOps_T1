# Team Guide — Exact Steps Per Person
# Worker Finder DevOps Assignment

## One-time Setup (Person 1 does this FIRST, others follow)

### Person 1: Create the GitHub Repository
```bash
# 1. Go to github.com → New Repository
#    Name: worker-finder-devops
#    Visibility: Public
#    DO NOT initialize with README (we'll push our own)

# 2. On your laptop, in the workerfinder/ folder:
git init
git add .
git commit -m "Initial commit: Worker Finder full project"
git branch -M main
git remote add origin https://github.com/YOUR_USERNAME/worker-finder-devops.git
git push -u origin main

# 3. Create dev branch
git checkout -b dev
git push -u origin dev

# 4. On GitHub: Settings → Branches → Add rule for "main"
#    Check "Require pull request before merging" (optional but shows good practice)
```

### Person 2 & 3: Clone the repo
```bash
git clone https://github.com/YOUR_USERNAME/worker-finder-devops.git
cd worker-finder-devops
git checkout dev
```

---

## Person 1 — DevOps Engineer
**Files you own:** `.github/workflows/ci.yml`, `rest-api/Dockerfile`, `frontend/Dockerfile.frontend`, `docker-compose.yml`

### Week 1 Commits
```bash
git checkout dev
git pull origin dev
git checkout -b feature/dockerfile

# Copy Dockerfile into rest-api/Dockerfile
# Commit it:
git add rest-api/Dockerfile
git commit -m "add: Dockerfile for Spring Boot REST API (multi-stage build)"
git push origin feature/dockerfile

# Open Pull Request: feature/dockerfile → dev on GitHub
# Merge it yourself (or ask a teammate to review)

git checkout dev && git pull origin dev
git checkout -b feature/frontend-dockerfile

git add frontend/Dockerfile.frontend
git commit -m "add: Dockerfile for React frontend with nginx"
git push origin feature/frontend-dockerfile
# Open PR → dev, merge
```

### Week 2 Commits
```bash
git checkout dev && git pull origin dev
git checkout -b feature/github-actions

git add .github/workflows/ci.yml
git commit -m "add: GitHub Actions CI pipeline — build, test, docker, deploy"
git push origin feature/github-actions
# Open PR → dev, merge

git checkout -b feature/docker-compose
git add docker-compose.yml
git commit -m "add: docker-compose for local full-stack development"
git push origin feature/docker-compose
# Open PR → dev, merge
```

### Week 3 — Docker Hub Setup
```bash
# 1. Create account at hub.docker.com
# 2. Create repository: worker-finder-api

# On GitHub repo: Settings → Secrets → Actions → New secret:
#   DOCKERHUB_USERNAME = your username
#   DOCKERHUB_TOKEN    = hub.docker.com → Account Settings → Security → New Token

# 3. Push to main to trigger the full pipeline:
git checkout main
git merge dev
git push origin main
# Watch the Actions tab on GitHub — all 4 jobs should go green!

# 4. Screenshot the green CI run for your report
```

### Demo Day Jenkins Setup
```bash
# Download Jenkins from jenkins.io/download → Generic Java (WAR)
java -jar jenkins.war --httpPort=8090
# Open http://localhost:8090
# Install suggested plugins + Maven Integration + Docker Pipeline
# Create Pipeline job → Git URL → Jenkinsfile
# Run it and screenshot
```

---

## Person 2 — Backend Engineer
**Files you own:** `rest-api/src/test/java/com/workerfinder/restapi/WorkerModelTest.java`,
`rest-api/src/test/java/com/workerfinder/restapi/WorkerControllerLogicTest.java`

### Week 1 Commits
```bash
git checkout dev && git pull origin dev
git checkout -b feature/worker-model-tests

# Copy WorkerModelTest.java into the test directory
git add rest-api/src/test/java/com/workerfinder/restapi/WorkerModelTest.java
git commit -m "add: unit tests for Worker model — constructor, defaults, setters"
git push origin feature/worker-model-tests
# Open PR → dev, merge
```

### Week 2 Commits
```bash
git checkout dev && git pull origin dev
git checkout -b feature/controller-logic-tests

git add rest-api/src/test/java/com/workerfinder/restapi/WorkerControllerLogicTest.java
git commit -m "add: unit tests for filtering, job status, and rating logic"
git push origin feature/controller-logic-tests
# Open PR → dev, merge

# Run tests locally to verify they pass:
cd rest-api
mvn test
# Should show: Tests run: 23, Failures: 0, Errors: 0
```

### Week 3 — Fix pom.xml test package issue
```bash
git checkout dev && git pull origin dev
git checkout -b fix/test-package-name

# The existing RestApiApplicationTests.java uses wrong package name
# Edit it: change "com.workerfinder.rest_api" → "com.workerfinder.restapi"
# Also remove the @SpringBootTest annotation (it tries to load full context + RMI)
# Replace with a simple placeholder

git add rest-api/src/test/java/com/workerfinder/rest_api/RestApiApplicationTests.java
git commit -m "fix: correct test package name and remove RMI-dependent context load"
git push origin fix/test-package-name
# Open PR → dev, merge
```

---

## Person 3 — RMI Server + Documentation
**Files you own:** `README.md`, `docs/project-report.md`, screenshots

### Week 1 Commits
```bash
git checkout dev && git pull origin dev
git checkout -b feature/readme

git add README.md
git commit -m "add: README with architecture, setup instructions, API endpoints"
git push origin feature/readme
# Open PR → dev, merge
```

### Week 2 Commits
```bash
git checkout dev && git pull origin dev
git checkout -b feature/gitignore

git add .gitignore
git commit -m "add: .gitignore for Maven, Node, IDE, Docker artifacts"
git push origin feature/gitignore
# Open PR → dev, merge

git checkout -b feature/project-report
# Write the project report in docs/project-report.md
git add docs/
git commit -m "add: project report — problem definition, architecture, tools used"
git push origin feature/project-report
# Open PR → dev, merge
```

### Week 3 — Screenshots for Submission
Take screenshots of:
1. GitHub Actions "all green" pipeline run
2. Docker Hub showing the pushed image
3. Render.com showing the live service
4. Jenkins pipeline (if set up)
5. Test results showing all tests passed

Save them in `docs/screenshots/` and commit:
```bash
git checkout -b feature/screenshots
git add docs/screenshots/
git commit -m "add: CI/CD pipeline screenshots and deployment evidence"
git push origin feature/screenshots
```

---

## Final Submission Checklist

- [ ] GitHub repo is public and has 10+ commits spread across 3 people
- [ ] `main`, `dev`, and `feature/...` branches visible in repo
- [ ] `.github/workflows/ci.yml` is present and ran at least once (green)
- [ ] `rest-api/Dockerfile` is present
- [ ] Tests pass (`mvn test` shows 0 failures)
- [ ] Docker image pushed to Docker Hub
- [ ] App deployed on Render (screenshot or live URL)
- [ ] Jenkins demo ready (Jenkinsfile + screenshot)
- [ ] README.md complete
- [ ] Project report written
- [ ] Screenshots folder in repo

## What to Submit to Teacher
1. GitHub repository link
2. Docker Hub image link
3. Live Render URL (or screenshots)
4. Project report PDF
5. Screenshot of GitHub Actions green run
6. Screenshot of Jenkins run (for demo)
