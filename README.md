# learn-cicd-starter (Notely)

![Tests](https://github.com/gooneraki/learn-cicd-starter/actions/workflows/ci.yml/badge.svg)

This repo contains the starter code for the "Notely" application for the "Learn CICD" course on [Boot.dev](https://boot.dev).

## Local Development

Make sure you're on Go version 1.22+.

Create a `.env` file in the root of the project with the following contents:

```bash
PORT="8080"
```

Run the server:

```bash
go build -o notely && ./notely
```

*This starts the server in non-database mode.* It will serve a simple webpage at `http://localhost:8080`.

You do *not* need to set up a database or any interactivity on the webpage yet. Instructions for that will come later in the course!

## About This Repo

This repo was forked from Boot.dev's starter code for the [Learn CI/CD with GitHub Actions, Docker and Go](https://www.boot.dev/courses/learn-ci-cd-github-docker-golang) course. It's used here to build out and practice a full CI/CD pipeline: testing, linting, security scanning, Docker builds, and deployment to Google Cloud Run with a Turso database.

Most of the course content and lesson-by-lesson instructions are captured in [COURSE_NOTES.md](./COURSE_NOTES.md), kept for future reference when revisiting this repo.