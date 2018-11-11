---
layout: post
title: 'Crontab과 scp를 이용한 데이터베이스 백업 자동화'
date: 2018-11-05 19:30:37 +0900
categories: database backup crontab scp
---

드리머리 서비스 베타 테스트 중, 프론트 서버가 테스트 api서버에 연결된 걸 모르고 디비가 날아간 줄 알고 1시간동안 헤맸던 적이 있다. 다시 제대로 연결한 뒤 데이터베이스가 그대로 있는 걸 보고 느꼈던 안도감이란... <br><br>
다시는 그런 일이 일어나지 않기 위해 백업 자동화를 구현하고자 했다.
우선 해당 ec2 서버 인스턴스 로컬에 백업본을 만들고, 다른 ec2 인스턴스에도 원격으로 백업본을 저장하기로 결정했다.<br><br>
우리 서비스는 몽고DB를 사용하고 있기에 커맨드라인 명령어 한 줄로 간단하게 백업DB파일을 만들 수 있다.

```console
mongodump -h <백업 DB가 설치된 IP주소:PORT번호> -d <백업할 DB 이름> -o <백업 BSON 파일 저장 경로>
```

백업 파일은 BSON형식으로 저장된다.

```console
mongodump -h 127.0.0.1:27017 -d dreamary -o ~/backup
```

복구시에는 mongostore 를 사용한다.

```console
mongostore -h 127.0.0.1:27017 -d dreamary2 -o ~/backup
```

이제 이 백업을 1시간마다 자동으로 하게 만들어야 한다. 우분투의 내장 프로그램 중 특정 시간에 특정 작업을 할 수 있게 해주는 crontab이라는 프로그램을 사용하자.

```console
crontab -l
```

위와 같은 명령은 현재 crontab에 등록된 작업 리스트를 보여준다. 새로운 작업을 하고 싶으면 -e 옵션을 사용하자.

```console
crontab -e
```

그러면 에디터로 crontab 작업 리스트를 편집할 수 있다.

```console
* * * * * ls
```

다섯개의 \* 가 실행 주기를 나타낸다. 앞에서부터 분(0-59), 시(0-23), 일(1-31), 월(1-12), 요일(0-7) 을 의미한다. 즉 위의 내용은 매분 ls 명령어를 실행하라는 의미이다.<br>

```console
0 * * * * mongodump -h 127.0.0.1:27017 -d dreamary -o ~/backup
```

위와 같이 등록하면 매시 0분마다 홈 디렉토리의 backup 디렉토리에 디비를 자동으로 백업하게 된다.<br><br>
이제 로컬 백업 자동화는 완료되었고, 원격지 서버에 백업파일을 전송하는 방법을 알아보자.<br>
파일을 네트워크를 통해 전송하는 방법은 여러가지가 있는데, 그 중 Secure Copy, 즉 scp 명령어를 이용하였다. 사용법은 아래와 같다.<br>
-i 옵션은 키 인증이 필요한 경우 키파일 경로를 줄 수 있고, -p 옵션으로 접속 포트를 설정할 수 있다(기본은 22번).

```console
scp [옵션] <원본 경로 및 파일> <접속계정@복사/전송 할 주소:경로>
```

ec2 원격지 서버에 백업 파일을 보내려면 인증이 필요하기에 우선 키 파일을 생성했다.

```console
원본 서버
ssh-keygen -t rsa
```

공개키와 개인키가 생성되었다.<br>
이제 공개키를 백업 원격지 서버로 전송한다.

```console
백업 파일이 있는 서버도 접근 인증이 필요해 -i 옵션으로 키파일을 제공하였다.
scp -i dreamarykey.pem /.ssh/id_rsa.pub ubuntu@서버주소:~/
```

백업 원격지 서버에 공개키가 잘 전송되었다면, 원격지 서버의 authorized_keys 에 해당 공개키를 추가해주면 된다.

```console
원격지 서버
cat id_res.pub >> ./.ssh/authorized_keys
```

보안을 위해 등록된 키는 삭제해주자.

```console
rm id_res.pub
```

이제 crontab으로 매시 30분에 백업 디비 파일을 원격지 서버로 복사/전송하는 작업을 설정해주면 된다!

```console
원본 서버
crontab -e
0 * * * * mongodump -h 127.0.0.1:27017 -d dreamary -o ~/backup
30 * * * * scp -i ~/.ssh/id_rsa ~/backup ubuntu@원격지서버주소:~/backup
```
