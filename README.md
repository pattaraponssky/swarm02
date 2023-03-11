# SWARM02
## Ref awaresome-compose
* https://github.com/docker/awesome-compose/tree/master/nginx-nodejs-redis

## WakaTime Project
* https://wakatime.com/@spcn11/projects/peiixhrzsu

## URL
* https://spcn11swarm02.xops.ipv9.me/

## ขั้นตอนการสร้าง

- เลือก awesome-compose ที่ต้องการแล้วทำการ clone มาแก้ไขใน vscode

   - https://github.com/docker/awesome-compose

- เปิดไฟล์งานขึ้นมา คลิกขวาที่ Dockerfile

- build images จากตัว Dockerfile ทั้งสองตัวแล้วตั้งชื่อ

![image](https://user-images.githubusercontent.com/113360594/224477756-2ffc6121-fbb5-4be0-962c-10beadf07996.png)

- เข้าสู่ระบบ docker hub โดยใช้คำสั่ง 

      docker login

- แล้ว push ขึ้น docker hub ของเราทั้งสอง image

      docker push pattaraponssky/swarm02-web:1.0

      docker push pattaraponssky/swarm02-nginx:1.0

### STACK DEPLOY

 - สร้างไฟล์คำสั่งไว้

            version: '3'
            services:
            redis:
                image: 'redislabs/redismod'
                networks:
                - backend
            web1:
                image: 'pattaraponssky/swarm02-web:1.0'
                hostname: web1
                networks:
                - backend
            web2:
                image: 'pattaraponssky/swarm02-web:1.0'
                hostname: web2
                networks:
                - backend
            nginx:
                image: 'pattaraponssky/swarm02-nginx:1.0'
                depends_on:
                - web1
                - web2
                networks:
                - backend
                - webproxy
                deploy:
                    replicas: 1
                    labels:
                    - traefik.docker.network=webproxy
                    - traefik.enable=true
                    - traefik.constraint-label=webproxy
                    - traefik.http.routers.spcn11swarm02-https.entrypoints=websecure
                    - traefik.http.routers.spcn11swarm02-https.rule=Host("spcn11swarm02.xops.ipv9.me")
                    - traefik.http.routers.spcn11swarm02-https.tls.certresolver=default
                    - traefik.http.services.spcn11swarm02.loadbalancer.server.port=80
                    - traefik.http.routers.spcn11swarm02-https.tls=true
                    resources:
                    reservations:
                        cpus: '0.1'
                        memory: 10M
                    limits:
                        cpus: '0.4'
                        memory: 250M

            networks:
            webproxy:
                external: true
            backend:
                driver: overlay


- deploy stack ที่ https://portainer.ipv9.me/ 



- ตรวจสอบ url ที่ใช้งาน
   
   - https://spcn11swarm02.xops.ipv9.me/

