# Run Portainer on Current Directory
####
    git clone https://github.com/SumonPaul18/portainer-nfs.git
    cd portainer-nfs
    docker compose up -d
    docker compose ps

Broswing Portainer Dashboard:<br>
http://dockerhostip:9000

# Run Portainer and Data Store in NFS Server

####
    mkdir portainer && cd portainer
    cat <<EOF | sudo tee docker-compose.yml
    version: "2.2"
    services:
      portainer:
        image: portainer/portainer-ce:latest
        container_name: portainer
        restart: always
        ports:
          - "8000:8000"
          - "9000:9000"
        volumes:
          - /var/run/docker.sock:/var/run/docker.sock
          - nfsvolume-portainer:/data:rw
        tty: true
    volumes:
      nfsvolume-portainer:
        driver: local
        driver_opts:
          type: "nfs"
          o: "addr=192.168.0.96,rw,nfsvers=4"
          device: ":/nfs-share/docker/portainer-data"
    EOF
####
Now, Run Docker Compose UP Command
####
    docker compose up -d
####
