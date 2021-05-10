# nginx setting
## linux ubuntu 18.04

> nginx설치  

  sudo apt install nginx

> Let’s Encrypt 설치

  sudo apt-get install certbot

> 인증서 생성

  #다음 command 실행 시 출력되는 fullchain.pem, privkey.pem 파일 경로 기록 해 두기
  sudo certbot certonly --webroot -w . -d your_domain.com

> 인증서 자동갱신 설정
 
  sudo vi /etc/crontab 
  # 파일에 다음 내용을 추가
  0 0 1 * * /usr/bin/certbot renew --renew-hook=service nginx restart

> nginx 설정 파일 작성

  sudo cd /etc/nginx/sites-available
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
