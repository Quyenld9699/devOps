# Build jenkins with docker

## docker-compose file

```yml
version: "3"

services:
    jenkins:
        image: jenkins/jenkins:lts
        container_name: jenkins
        ports:
            - "8181:8080"
            - "50000:50000"

        volumes:
            - ./jenkins:/var/jenkins_home
        user: root
    nginx:
        image: nginx:latest
        container_name: nginx-jenkins
        ports:
            - "80:80"
            - "443:443"
        volumes:
            - ./nginx/nginx.conf:/etc/nginx/conf.d/jenkins.conf
            - /path/to/certs:/etc/certs
```

## nginx.conf file

```conf
server {
    listen 443 ssl;
    server_name jenkins.quyenvip.click;

    ssl_certificate /etc/certs/domain.crt;
    ssl_certificate_key /etc/certs/domain.key;

    location / {
            proxy_pass http://[hostname -i]:8181;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-Proto $scheme;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_ssl_verify off;
    }
}
```

This config nginx alow **ssl** for jenkins
