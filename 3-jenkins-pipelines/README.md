# Exercise 3 - Jenkins Pipelines

**Goal**: Create simple Jenkins pipelines, execute them, and explore workspace and artifact storage

## Prerequisites

- Jenkins controller and agents running in Docker Compose (exercises 1 and 2)

## Steps

### 1. Create First Pipeline

1. In Jenkins UI, click "New Item"
2. Enter name: `simple-pipeline`
3. Select "Pipeline"
4. In the pipeline script box, paste content from `pipelines/Jenkinsfile.simple`
5. Click "Save"
6. Click "Build Now"

### 2. Create Pipeline with Workspace Demo

1. Create new item: `workspace-demo`
2. Use `pipelines/Jenkinsfile.workspace`
3. Build and explore the workspace

### 3. Create Pipeline with Multiple Agents

1. Create new item: `agent-pipeline`
2. Use `pipelines/Jenkinsfile.agents`
3. Build and observe which agent each stage runs on

## Explore Workspace

### Where is the workspace?

The workspace is stored on the agent (or controller if using `agent any`):

```bash
# On the Jenkins controller container
docker exec jenkins-controller ls -la /var/jenkins_home/workspace/
```

Each job gets its own directory:
```
/var/jenkins_home/workspace/
├── simple-pipeline/
│   ├── build.info
│   └── output/
│       ├── artifact1.txt
│       └── artifact2.txt
├── workspace-demo/
└── agent-pipeline/
```

### Inside a running build

During a build, Jenkins also creates a temporary directory:
```
/var/jenkins_home/workspace/simple-pipeline@script/  # Pipeline script
/var/jenkins_home/workspace/simple-pipeline@tmp/     # Temporary files
```

### Get workspace path from build

In Jenkins UI:
1. Open a build
2. Click "Console Output"
3. Look for: `Running on Jenkins` or `Running on agent-01`
4. The workspace path is: `/var/jenkins_home/workspace/<job-name>/`

## Explore Artifacts

### Where are artifacts stored?

```bash
docker exec jenkins-controller ls -la /var/jenkins_home/artifacts/
```

Artifact structure:
```
/var/jenkins_home/artifacts/
├── <job-name>/
│   └── <build-number>/
│       ├── build.info
│       └── output/
│           ├── artifact1.txt
│           └── artifact2.txt
```

### View in Jenkins UI

1. Open a build
2. Look for "Artifacts" section (right sidebar)
3. Click to download archived files

### Archive configuration in Jenkinsfile

```groovy
archiveArtifacts artifacts: 'build.info,output/*.txt', fingerprint: true
```

- `fingerprint: true` - tracks artifact usage across builds

## Jenkins Home Directory Structure

```
/var/jenkins_home/
├── jobs/                  # Job configurations
├── workspace/             # Build workspaces
├── artifacts/            # Archived artifacts
├── builds/                # Build history
├── plugins/               # Installed plugins
├── secrets/              # Secrets and keys
├── users/                # User configurations
└── logs/                 # Log files
```

## Cleanup



To clean old builds:

```bash
# Inside Jenkins container
docker exec jenkins-controller rm -rf /var/jenkins_homeworkspace/*
docker exec jenkins-controller rm -rf /var/jenkins_home/artifacts/*
```

Or use Jenkins UI: Job > "Discard Old Builds"

```sh
docker compose -p jenkins down
```

## Want to do more? Try this

1. **Modify simple-pipeline**: Add a stage that creates a file in the workspace
2. **Archive custom artifacts**: Create additional files and archive them
3. **Distribute work**: Modify agent-pipeline to use all 5 agents in parallel
4. **Explore workspace**: Use `docker exec` to navigate the workspace and find your artifacts
