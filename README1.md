# Build, publish, pull, and run the Docker image

This guide builds the image from this repository's `Dockerfile`, tags it as
`v6.0`, publishes it to your Docker Hub account, then pulls and runs that
published image.

> **Important:** Docker Hub image names include your Docker Hub username:
> `<DOCKERHUB_USERNAME>/fred-ndabikunze-juice-ship:v6.0`. The shorter name
> `fred-ndabikunze-juice-ship:v6.0` is only a local image name; it is not a
> complete Docker Hub address.

## Before you start

- Install Docker Desktop or Docker Engine, and make sure its daemon is running.
- Create/sign in to a Docker Hub account.
- Create a Docker Hub repository named `fred-ndabikunze-juice-ship` (or let
  Docker create it when publishing, if your account permits that).
- Run the commands below from the root of this repository, where the
  `Dockerfile` is located.
- Replace `YOUR_DOCKERHUB_USERNAME` in each command with your actual Docker Hub
  username. Do not include angle brackets.

## 1. Build and tag the image

Build the image and give it the requested local name and version:

```bash
docker build -t fred-ndabikunze-juice-ship:v6.0 .
```

Tag it with the fully qualified Docker Hub name (replace the username):

```bash
docker tag fred-ndabikunze-juice-ship:v6.0 YOUR_DOCKERHUB_USERNAME/fred-ndabikunze-juice-ship:v6.0
```

Confirm that both tags are present:

```bash
docker image ls
```

## 2. Sign in and push to Docker Hub

```bash
docker login --username YOUR_DOCKERHUB_USERNAME
```

Follow Docker's prompt to authenticate. Use a Docker Hub access token if
prompted; do not put your password or token directly in a command or this file.

Push the fully qualified tag:

```bash
docker push YOUR_DOCKERHUB_USERNAME/fred-ndabikunze-juice-ship:v6.0
```

After the push completes, the image should be visible in the
`fred-ndabikunze-juice-ship` repository on Docker Hub.

## 3. Pull the published image

To confirm the image can be downloaded from Docker Hub:

```bash
docker pull YOUR_DOCKERHUB_USERNAME/fred-ndabikunze-juice-ship:v6.0
```

## 4. Run the pulled image

Run the published image and expose the app only on this computer:

```bash
docker run --rm --name juice-shop-pulled -p 127.0.0.1:3000:3000 YOUR_DOCKERHUB_USERNAME/fred-ndabikunze-juice-ship:v6.0
```

Open <http://localhost:3000>. Stop the container with **Ctrl+C** in the
terminal. The `--rm` option removes the stopped container; it does not remove
the image.

## Deploy with Jenkins

The root-level `Jenkinsfile` pulls
`fredndabikunze01/fred-ndabikunze-juice-ship:v6.0`, replaces the
`juice-shop-jenkins` container, and checks that the app responds on port `3000`.

1. Configure a Jenkins Pipeline job to use this repository's `Jenkinsfile`.
2. Run the job on a persistent Jenkins agent that shares a host with its Docker
   Engine, and has `curl` installed. The Docker Hub image must be public, or
   Docker Hub credentials must already be configured on that agent.
3. After a successful build, open `http://localhost:3000` on the agent host.

The pipeline binds the app to `127.0.0.1` so it is not exposed to the network.
Change `HOST_PORT` in the `Jenkinsfile` if port `3000` is already in use.
Juice Shop is intentionally vulnerable; keep it on a trusted, isolated
machine or network.

## Troubleshooting

- **Cannot connect to Docker:** Start Docker Desktop or the Docker Engine, then
  retry.
- **Access denied while pushing:** Check the username, confirm you are logged
  in with `docker login`, and make sure your account can push to that
  repository.
- **Port 3000 is already in use:** Change the left side of the port mapping,
  for example `-p 127.0.0.1:3001:3000`, and browse to
  <http://localhost:3001>.
- **Repository is not found while pulling:** Confirm the repository exists,
  the name and username are correct, and that the repository is public or you
  are logged in with access.

## Safety note

OWASP Juice Shop is intentionally vulnerable and is designed for security
training. Keep it on a trusted, isolated machine or network. Do not expose it
to the public internet or use it to store real personal or payment data.
