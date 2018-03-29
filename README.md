# About

Docker image based on [slawekkolodziej/php-5.3-ioncube](https://github.com/slawekkolodziej/php-5.3-ioncube) which based on eugeneware/php-5.3.

It adds support for:
- Ioncube loader
- Sending emails via Exim (based on https://github.com/namshi/docker-smtp)
- Optionally running Cron

# Commands

* 기본 실행 명령: supervisord which then starts nginx, php-fpm and exim.

```bash
# see Dockerfile.
["/usr/bin/supervisord", "-c", "/etc/supervisor/conf.d/supervisord-web.conf"]
```

## 실행 팁

* 이 도커를 빌드 하는 방법 (우선, docker 설치 필요)

```bash
$ git clone git@github.com:lemoncloud-io/php-5.3-ioncube.git
$ cd php-5.3-ioncube
$ sudo docker build -t my-php-app .
```

* 기본 실행 명령어

```bash
$ sudo docker run -it --rm --name my-running-app my-php-app
```

* 여러가지 실행 방법

```bash
# 현재 위치 "$PWD"를 /var/www/html 에 연결 시켜서 실행.
$ docker run -it --rm --name my-running-app -v "$PWD":/var/www/html my-php-app

# 포트 포워딩으로 8000 -> 80 으로 설정하고, 초기 실행을 bash 쉘로 보이게.
$ docker run -it --rm --name my-running-app -v -p 8000:80 my-php-app /bin/bash

# 이건 뭐, 거의 운영 설정으로 웹서비스 소스가 있는 위치에서 실행.
$ docker run -it --rm --name my-running-app -v /var/www/html:/var/www/html -p 8000:80 my-php-app
```

# 참고

* [docker-php-5.3-apache](https://github.com/eugeneware/docker-php-5.3-apache)
* [docker-php-5.3](https://github.com/helderco/docker-php-5.3)

