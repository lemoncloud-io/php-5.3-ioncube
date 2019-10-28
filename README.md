# PHP-5.3-IONCUBE

Docker image based on [slawekkolodziej/php-5.3-ioncube](https://github.com/slawekkolodziej/php-5.3-ioncube) which based on eugeneware/php-5.3.

It adds support for:

- Ioncube loader
- Sending emails via Exim (based on https://github.com/namshi/docker-smtp)
- Optionally running Cron

## Commands

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
$ sudo docker build -t php53-ioncube .
```

* 기본 실행 명령어

```bash
$ sudo docker run -it --rm --name my-app php53-ioncube
```

* 여러가지 실행 방법

```bash
# 현재 위치 "$PWD"를 /var/www/html 에 연결 시켜서 실행.
$ docker run -it --rm --name my-running-app -v "$PWD":/var/www/html php53-ioncube

# 포트 포워딩으로 8000 -> 80 으로 설정하고, 초기 실행을 bash 쉘로 보이게.
$ docker run -it --rm --name my-running-app -v -p 8000:80 php53-ioncube /bin/bash
```


## AWS ECR Repository

- From AWS ECR -> `Create resposity` -> input `php53-ioncube`, then create.

```bash
# with aws 'lemon' profile, login docker.
$ $(aws ecr get-login --profile <profile> --no-include-email --region ap-northeast-2)

# Save Account-ID by profile
$ export AWS_NO=$(aws sts get-caller-identity --output text --query 'Account' --profile <profile>)

# tag your image with a keyword. (lemon)
$ docker tag php53-ioncube:latest "$AWS_NO.dkr.ecr.ap-northeast-2.amazonaws.com/php53-ioncube:latest"

# push image to repository
$ docker push "$AWS_NO.dkr.ecr.ap-northeast-2.amazonaws.com/php53-ioncube:latest"
```



## 참고

* [docker-php-5.3-apache](https://github.com/eugeneware/docker-php-5.3-apache)
* [docker-php-5.3](https://github.com/helderco/docker-php-5.3)
* [mcrypt 설치 문제시](https://taroth.kr/?p=5712)


---------------------
# 추가 업데이트 사항

## npay 암호화 지원을 위한 (mhash 설치) @180824

- npay는 `php option --with-mhash --with-mcrypt --with-dom --with-zlib-dir` 의 설정 필요함. (기본 hash가 안먹음 -_-;;)

```bash
$ cd /usr/src/php
$ ./configure --disable-cgi \
    $(command -v apxs2 > /dev/null 2>&1 && echo '--with-apxs2' || true) \
    --with-config-file-path="/usr/local/lib" \
    --with-config-file-scan-dir="/usr/local/lib/conf.d" \
    --with-mysql \
    --with-mysqli \
    --with-pdo-mysql \
    --with-openssl=/usr/local/ssl \
    --with-zlib \
    --with-zlib-dir=/lib/x86_64-linux-gnu \
    --enable-fpm \
    --with-fpm-user=www-data \
    --with-fpm-group=www-data \
    --disable-hash
$ make -j"$(nproc)"
$ make install
# $ dpkg -r bison libbison-dev
# $ apt-get purge -y --auto-remove autoconf2.13
$ make clean

$ docker-php-ext-configure hash --with-mhash=/usr
$ docker-php-ext-install hash
```

