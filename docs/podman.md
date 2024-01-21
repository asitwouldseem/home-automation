# Podman Instructions 
Remember to first chown, then unshare: `podman unshare chown 200:200 -R jellyfin` so that containers can run rootless.

## Logitech Media Server
```
podman run -d
	--hostname=lms.local
	--name=lms
	--network=host
	--restart=unless-stopped
	-v "/home/cameron/apps/lms/config":"/config":z
	-v "/home/cameron/apps/lms/playlist":"/playlist":z
	--mount type=bind,source=/mnt/music,destination=/music,ro=true
docker.io/lmscommunity/logitechmediaserver:stable
```

You will need to run `sudo setsebool virt_use_samba on` to keep SELinux happy. As well as ports 5353/udp, 65535/tcp and 4070/tcp for Spotify Connect.

## Jellyfin
```
podman run -d
	--hostname jellyfin.local
	--privileged 
	--name jellyfin
	--restart=unless-stopped
	-p 8096:8096/tcp
	-v /home/cameron/apps/jellyfin/cache:/cache:Z
	-v /home/cameron/apps/jellyfin/config:/config:Z
	--device /dev/dri/:/dev/dri/
	--mount type=bind,source=/mnt/books,destination=/books,ro=true
	--mount type=bind,source=/mnt/music,destination=/music,ro=true
	--mount type=bind,source=/mnt/movies,destination=/movies,ro=true
	--mount type=bind,source=/mnt/tv,destination=/tv,ro=true
docker.io/jellyfin/jellyfin:latest
```

## Pihole
Thank you, [Woody](https://b-woody.com/posts/2022-05-12-pihole-on-a-rootless-podman-container/)!
```
podman run -d
    --cap-add=NET_ADMIN
    --net=slirp4netns:port_handler=slirp4netns
    --name pihole
    -p 1053:53/tcp
    -p 1053:53/udp
    -p 8080:80
    -e TZ="Australia/Brisbane"
    -v "/home/cameron/apps/pihole:/etc/pihole"
    -v "/home/cameron/apps/dnsmasq.d:/etc/dnsmasq.d"
    --restart=unless-stopped
    --hostname pihole.local
    -e INTERFACE="tap0"
    --security-opt label=disable
docker.io/pihole/pihole:latest
```