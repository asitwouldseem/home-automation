# Podman Instructions 
Remember to first chown, then unshare: `podman unshare chown 200:200 -R jellyfin` so that containers can run rootless.

## Logitech Media Server
```
    podman run -d
    --hostname=lms.local
    --name=lms
    --network=host
    --restart=unless-stopped
    --device /dev/snd
    -v "/home/cameron/apps/lms/config":"/config":rw
    -v "/home/cameron/apps/lms/playlist":"/playlist":rw
    -v "/mnt/music":"/music":ro
    -v "/etc/localtime":"/etc/localtime":ro
    -v "/etc/timezone":"/etc/timezone":ro
    docker.io/lmscommunity/logitechmediaserver:stable
```

## Jellyfin
```
    podman run -d
    --hostname jellyfin.local
    --name jellyfin
    --restart=unless-stopped
    -p 8096:8096/tcp
    -v "/home/cameron/apps/jellyfin/cache":"/cache":rw
    -v "/home/cameron/apps/jellyfin/config":"/config":rw
    --mount type=bind,source=/mnt/books,destination=/books,ro=true
    --mount type=bind,source=/mnt/music,destination=/music,ro=true
    --mount type=bind,source=/mnt/movies,destination=/movies,ro=true
    --mount type=bind,source=/mnt/tv,destination=/tv,ro=true
    docker.io/jellyfin/jellyfin:latest
```

## xTeve
```
podman run -d
  --hostname xteve.local
  --name=xteve
  --net=host
  --restart=unless-stopped
  --log-opt max-size=10m
  --log-opt max-file=3
  -e TZ="Australia/Brisbane"
  -v "/home/cameron/apps/xteve/.xteve":"/root/.xteve":rw
  -v "/home/cameron/apps/xteve/config":"/config":rw
  -v "/home/cameron/apps/xteve/tmp":"/tmp/xteve":rw
  -v "/home/cameron/apps/xteve/data":"/TVH":rw
docker.io/alturismo/xteve:latest
```