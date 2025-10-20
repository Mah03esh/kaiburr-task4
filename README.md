# Task 4: CI/CD Pipeline - Kaiburr Assessment

## Overview

Complete CI/CD pipeline using **GitHub Actions** to automate build and deployment for the Kaiburr Task Management Application.

## Pipeline Objectives

- Automated build for Java Backend (Spring Boot) and React Frontend (TypeScript + Ant Design)
- Multi-stage Docker image creation for both services
- Parallel execution of Backend and Frontend CI jobs
- Artifact uploading with 7-day retention
- Automatic versioning using GitHub run numbers

## Architecture

### Repository Structure

```
task4/
├── .github/workflows/
│   └── ci-cd-pipeline.yml      # Main CI/CD workflow
├── screenshots/                 # 10 pipeline execution screenshots
├── README.md
└── .gitignore
```

### Related Repositories

- Backend (Task 1): https://github.com/mah03esh/kaiburr-task1
- Frontend (Task 3): https://github.com/mah03esh/kaiburr-task3
- CI/CD Pipeline (Task 4): https://github.com/mah03esh/kaiburr-task4


## Pipeline Workflow

### Triggers

- Push to `main` branch
- Pull Requests to `main` branch
- Manual dispatch (via GitHub UI)

### Jobs Overview

**Job 1: Backend CI** (ubuntu-latest)
1. Checkout Code → Setup JDK 17 → Maven Build → Docker Build → Upload Artifact

**Job 2: Frontend CI** (ubuntu-latest)
1. Checkout Code → Setup Node.js 20 → npm Build → Docker Build → Upload Artifact

**Job 3: Summary Report** (ubuntu-latest)
- Executes after both jobs complete
- Generates pipeline summary with job status and Docker image tags

## Docker Images

### Backend (Multi-Stage)
- **Build**: `maven:3.9-eclipse-temurin-17-alpine` → Maven build
- **Runtime**: `eclipse-temurin:17-jre-alpine` → Minimal JRE, port 8080

### Frontend (Multi-Stage)
- **Build**: `node:20-alpine` → npm build
- **Runtime**: `nginx:alpine` → Serves static files, port 80

## Image Tagging Strategy

Each run creates two tags per image:
- **Versioned**: `kaiburr-backend:42` (run number)
- **Latest**: `kaiburr-backend:latest`


## Pipeline Execution Screenshots

All screenshots demonstrate the complete CI/CD pipeline execution. Test scenarios follow Given-When-Then syntax for standardization.

### Screenshot 1: Backend Workflow Runs Overview

**Scenario: View Backend Pipeline Execution History**  
Given the CI/CD pipeline has been executed multiple times  
When the user navigates to the GitHub Actions tab  
Then a list of all backend workflow runs is displayed  
And each run shows the workflow name, status, branch, commit message, and execution time  
And the most recent runs appear at the top  

![Backend Workflow Runs](screenshots/01_backend_workflow_runs.png)

---

### Screenshot 2: Backend Workflow Details

**Scenario: View Detailed Backend Job Information**  
Given a backend workflow run has completed successfully  
When the user clicks on a specific workflow run  
Then detailed job information is displayed including execution times  
And the workflow summary shows all completed steps  
And artifacts section displays the generated JAR file  
And the total execution time is shown  

![Backend Workflow Details](screenshots/02_backend_workflow_details.png)

---

### Screenshot 3: Maven Build Output

**Scenario: Maven Compilation and Package Creation**  
Given the backend CI job has started  
When Maven executes `mvn clean package -DskipTests`  
Then the build logs show dependency resolution  
And source code compilation completes successfully  
And a JAR file is created in the target directory  
And the console displays "BUILD SUCCESS" message  

![Maven Build](screenshots/03_backend_maven_build.png)

---

### Screenshot 4: Backend Docker Build

**Scenario: Multi-Stage Docker Image Creation for Backend**  
Given the Maven build has completed successfully  
When the Docker build step executes  
Then a multi-stage Dockerfile builds the image  
And Stage 1 uses `maven:3.9-eclipse-temurin-17-alpine` as build base  
And Stage 2 uses `eclipse-temurin:17-jre-alpine` as runtime base  
And the final image is tagged with version number and "latest"  
And the build completes without errors  

![Backend Docker](screenshots/04_backend_docker_build.png)

---

### Screenshot 5: Backend Artifacts

**Scenario: Upload Backend Build Artifacts**  
Given the backend build and Docker image creation are complete  
When the upload artifact step executes  
Then the JAR file is uploaded to GitHub Actions  
And the artifact is named "backend-jar"  
And the artifact size is displayed (23.6 MB)  
And the retention period is set to 7 days  
And the artifact is available for download  

![Backend Artifacts](screenshots/05_backend_artifacts.png)

---

### Screenshot 6: Frontend Workflow Runs Overview

**Scenario: View Frontend Pipeline Execution History**  
Given the CI/CD pipeline has executed frontend builds  
When the user navigates to the GitHub Actions tab  
Then a list of all frontend workflow runs is displayed  
And each run shows status, branch, commit details, and timestamp  
And successful runs are indicated with green checkmarks  

![Frontend Workflow Runs](screenshots/06_frontend_workflow_runs.png)

---

### Screenshot 7: Frontend Workflow Details

**Scenario: View Detailed Frontend Job Information**  
Given a frontend workflow run has completed  
When the user opens a specific workflow run  
Then the job details page displays all execution steps  
And timing information for each step is shown  
And the artifacts section shows the frontend build output  
And the total workflow duration is displayed  

![Frontend Workflow Details](screenshots/07_frontend_workflow_details.png)

---

### Screenshot 8: npm Build Output

**Scenario: Vite Build Process for React Application**  
Given the frontend CI job has started  
When npm executes `npm ci && npm run build`  
Then dependencies are installed cleanly  
And Vite builds the production bundle  
And the dist/ directory is created with optimized files  
And the console shows successful build completion  
And the bundle size is displayed  

![npm Build](screenshots/08_frontend_npm_build.png)

---

### Screenshot 9: Frontend Docker Build

**Scenario: Multi-Stage Docker Image Creation for Frontend**  
Given the npm build has completed successfully  
When the Docker build step executes  
Then a multi-stage Dockerfile builds the image  
And Stage 1 uses `node:20-alpine` to build the React app  
And Stage 2 uses `nginx:alpine` to serve static files  
And the built files are copied from build stage to runtime stage  
And the final image is tagged with version and "latest"  

![Frontend Docker](screenshots/09_frontend_docker_build.png)

---

### Screenshot 10: Frontend Artifacts

**Scenario: Upload Frontend Build Artifacts**  
Given the frontend build and Docker image creation are complete  
When the upload artifact step executes  
Then the dist/ directory is uploaded to GitHub Actions  
And the artifact is named "frontend-dist"  
And the artifact is available for download from the workflow run  
And the retention period is set to 7 days  

![Frontend Artifacts](screenshots/10_frontend_artifacts.png)


## Testing & Validation

Pipeline tested with multiple executions. Test scenarios follow Given-When-Then syntax.

**Scenario 1: Automatic Pipeline Trigger on Push**  
Given code changes are committed to the main branch  
When a push event occurs  
Then the CI/CD pipeline automatically starts both backend and frontend jobs in parallel  

**Scenario 2: Backend Maven Build**  
Given the backend job is triggered  
When Maven executes `mvn clean package -DskipTests`  
Then a JAR file is successfully created and uploaded as an artifact  

**Scenario 3: Frontend npm Build**  
Given the frontend job is triggered  
When npm executes `npm ci && npm run build`  
Then the production build is created in the dist/ directory  

**Scenario 4: Parallel Job Execution**  
Given a push to main triggers the pipeline  
When both backend and frontend jobs start  
Then they execute simultaneously on separate runners  
And both complete within 4-6 minutes total  

**Scenario 5: Artifact Management**  
Given both builds complete successfully  
When the upload artifact steps execute  
Then artifacts are stored with 7-day retention and available for download  

## Usage Instructions

### Viewing Pipeline Results

1. Navigate to your GitHub repository
2. Click on the **Actions** tab
3. Select **Kaiburr CI/CD Pipeline** from workflows list
4. View recent runs and their status
5. Click on any run to see detailed logs

### Triggering the Pipeline

**Automatic:**
```bash
git add .
git commit -m "feat: update code"
git push origin main
```

**Manual:** Actions tab → "Kaiburr CI/CD Pipeline" → "Run workflow"

### Downloading Artifacts

1. Open completed workflow run
2. Scroll to "Artifacts" section
3. Download `backend-jar` or `frontend-dist`

## Local Testing

### Test Backend Docker Image

```bash
# Navigate to Task 1 directory
cd ../task1

# Build Docker image
docker build -t kaiburr-backend:local .

# Run container
docker run -p 8080:8080 kaiburr-backend:local

# Test endpoint
curl http://localhost:8080/api/tasks
```

### Test Frontend Docker Image

```bash
# Navigate to Task 3 directory
cd ../task3

# Build Docker image
docker build -t kaiburr-frontend:local .

# Run container
docker run -p 3000:80 kaiburr-frontend:local

# Open browser
start http://localhost:3000
```

## Configuration

### Environment Variables

| Variable | Value | Usage |
|----------|-------|-------|
| `BACKEND_IMAGE` | kaiburr-backend | Backend Docker image name |
| `FRONTEND_IMAGE` | kaiburr-frontend | Frontend Docker image name |
| `github.run_number` | Auto-incremented | Image version tag |
| `github.repository_owner` | mah03esh | Docker registry namespace |

### Workflow Inputs

The workflow uses:
- **JDK 17** (Temurin distribution) with Maven 3.9+
- **Node.js 20** (LTS version) with npm
- **Docker Engine** (pre-installed on ubuntu-latest)

### Cache Strategy

- **Maven**: Dependencies cached via `setup-java@v4`
- **npm**: Dependencies cached via `setup-node@v4`
- Caches persist across workflow runs for faster builds

## Pipeline Metrics

### Execution Times

| Job | Average Duration |
|-----|------------------|
| Backend CI | 2-3 minutes |
| Frontend CI | 1.5-2.5 minutes |
| Summary | < 30 seconds |
| **Total** | **~4-6 minutes** |

### Resource Usage

- **Runners**: 2 concurrent ubuntu-latest instances
- **Storage**: ~100-150MB per workflow run (artifacts)
- **Retention**: Artifacts kept for 7 days

## Learning Outcomes

This CI/CD pipeline demonstrates:

1. GitHub Actions workflow creation and configuration
2. Parallel job execution for faster builds
3. Multi-stage Docker builds for optimized images
4. Caching strategies for dependency management
5. Artifact management with retention policies
6. Automated versioning using run numbers
7. Status reporting and pipeline summaries

## Troubleshooting

### Build Failures

**Maven build fails:**
```bash
# Check Java version
java -version

# Check Maven version
mvn -version

# Verify dependencies
mvn dependency:tree
```

**npm build fails:**
```bash
# Clear npm cache
npm cache clean --force

# Reinstall dependencies
rm -rf node_modules && npm install
```

### Docker Build Issues

**Build context too large:**
```
# Ensure .dockerignore exists with:
node_modules
target
.git
*.log
```

**Image not found:**
```bash
# List Docker images
docker images

# Verify build completed
docker ps -a

# Check build logs
docker logs <container_id>
```

### Permission Issues

**Permission denied in workflow:**
```yaml
# Add execute permissions
- name: Make script executable
  run: chmod +x script.sh
```

## References

- [GitHub Actions Documentation](https://docs.github.com/en/actions)
- [Docker Multi-Stage Builds](https://docs.docker.com/build/building/multi-stage/)
- [Maven Central Repository](https://mvnrepository.com/)
- [npm Registry](https://www.npmjs.com/)

## Author

**Mahesh** - GitHub: [@mah03esh](https://github.com/mah03esh) - [kaiburr-task4](https://github.com/mah03esh/kaiburr-task4)

## License

This project is part of the Kaiburr Assessment and is for educational purposes.


