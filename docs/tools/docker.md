# Docker

## Docker Commands

- `docker run`: start a container.
- `docker ps`: list all running containers.
    - `docker ps -a`.
- `docker stop`: stop running.
- `docker rm`: remove a stop or exit container permantely.
- `docker images`: list images.
    - `docker rmi`: remove image
- `docker pull`: download an image.

!!! note

    container lives as long as the process.

- append a command: `docker run ubuntu sleep 5`.
- `docker exec distracted_mcclintock cat /etc/hosts`: execute a command (exec).
- `docker run -d`: 在后台运行