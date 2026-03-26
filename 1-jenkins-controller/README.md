# Exercise 1 - Jenkins Controller

**Goal**: Bring up Jenkins controller locally using Docker Compose

## Task: Configure Jenkins control plane

1. Create a `docker-compose.yml` file to start a Jenkins instance in docker (you can use the docker compose scaffold as a starting point)
  - Only one service is required: `jenkins`
  - Use image: `jenkins/jenkins:lts`
  - Expose ports: 8080 and 50000
  - Add a docker-managed volume and map it to `/var/jenkins_home`
2. Start Jenkins with: `docker compose -p jenkins up`
3. Wait for the system to boot up. The password will be shown in the console
4. Access `http://127.0.0.1:8080` and login
5. Go through the configuration wizard (create admin user)
6. Add the plugins that were covered in the lesson:
  - Gerrit Trigger
  - Generic Webhook trigger
7. Stop Jenkins: `docker compose -p jenkins down`

## Cleanup

```bash
docker compose down
```

## Notes

- Jenkins data is persisted in the `jenkins_home` Docker volume (and the `-p` project name parameter)
- Port 8080: Jenkins web interface
- Port 50000: JNLP port for agents
