# nginx setting, 무료 ssl설정
## linux ubuntu 18.04

프로젝트 배포 후 index 파일이 존재하는 경로 메모.
이하 배포경로라 칭함

> nginx설치  

    sudo apt install nginx

> Let’s Encrypt발급용 기본 사이트 설치

    sudo cp /etc/nginx/sites-available /etc/nginx/sites-available-bak -r
    sudo cp /etc/nginx/sites-available /etc/nginx/sites-enabled-bak -r
    sudo rm /etc/nginx/sites-available/*
    sudo rm /etc/nginx/sites-enabled/*
    cd /etc/nginx/sites-available/
    sudo touch [설정파일명]
    sudo ln -s /etc/nginx/sites-available/[설정파일명] /etc/nginx/sites-enabled/[설정파일명]
    sudo vi [설정파일명]
    
> vi를 통해 열린 설정 파일에 다음 설정 값 입력

    server {
        listen 80;
        location / {
            root   [배포경로];
            index  index.html index.htm;
            try_files $uri /index.html;
        }
    }

> nginx start

    sudo service nginx stop
    sudo service nginx start

> Let’s Encrypt 설치

    sudo apt-get install certbot

> 인증서 생성

    # 다음 command 실행 시 출력되는 fullchain.pem, privkey.pem 파일 경로 기록 해 두기
    sudo certbot certonly --webroot -w . -d your_domain.com

> 인증서 자동갱신 설정
 
    sudo vi /etc/crontab 
    # 파일에 다음 내용을 추가
    0 0 1 * * /usr/bin/certbot renew --renew-hook=service nginx restart

> nginx 설정 파일 작성

    cd /etc/nginx/sites-available
    sudo touch [file_name]
    sudo vi file_name
  
> [file_name]파일의 내용 작성
 
    server {
      listen 80 default_server;
      server_name your_domain.com;
      return 301 https://$server_name$request_uri;
    }

    server {
      listen 443 ssl;
      server_name your_domain.com;

      ssl_certificate fullchain.pem파일경로;
      ssl_certificate_key privkey.pem파일경로;

      location / {
          root 배포 된 app path;
          index index.html index.htm;
      }
    }
  
> nginx service 등록
 
    sudo ln -s /etc/nginx/sites-available/file_name /etc/nginx/sites-enabled/file_name

> nginx service 시작
 
    sudo service nginx stop
    sudo service nginx start
