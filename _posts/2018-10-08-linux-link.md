---
layout: post
title: 'Linux - 하드링크 & 소프트(심볼릭)링크'
date: 2018-10-05 23:30:37 +0900
categories: linux commandline link
---

윈도우나 맥에는 일반적인 파일에 대한 링크를 의미하는 바로가기 파일이 존재한다. 리눅스에서도 이와 비슷한 기능을 하는 파일이 있는데, 하드 & 소프트(심볼릭) 링크 파일이 그것이다.<br>

## 리눅스 inode

링크 파일을 이해하려면 먼저 리눅스의 inode 라는 개념을 이해해야 한다. 리눅스에서 파일은 실제 데이터와 파일 권한, 실제 위치등의 메타데이터를 저장한 inode 파일로 이루어져 있다. 리눅스에서 파일 이름은 해당 파일의 inode 를 가르키는 포인터라고 생각하면 된다.

<img src="/assets/images/linux-link.jpeg">

```javascript
ls -li
4304662636 -rw-r--r--   1 sangwoo  staff  1110  9 27 22:06 Gemfile
4304662645 -rw-r--r--   1 sangwoo  staff  6803  9 27 22:07 Gemfile.lock
4304662633 -rw-r--r--   1 sangwoo  staff  2179  9 29 01:32 _config.yml
```

위와 같은 디렉터리에서 ln 명령어로 하드링크를 만들어보자.

```javascript
ln Gemfile Gemfile-hard

ls -li
4304662636 -rw-r--r--   2 sangwoo  staff  1110  9 27 22:06 Gemfile
4304662636 -rw-r--r--   2 sangwoo  staff  1110  9 27 22:06 Gemfile-hard
4304662645 -rw-r--r--   1 sangwoo  staff  6803  9 27 22:07 Gemfile.lock
4304662633 -rw-r--r--   1 sangwoo  staff  2179  9 29 01:32 _config.yml
```

하드 링크와 원본이 같은 inode 넘버(리스트 맨 앞의 숫자들, -i 옵션으로 볼 수 있다)를 가지는것을 확인할 수 있고, 하드 링크가 생성되면서 해당 inode 를 참조하는 링크의 수(권한 문자열과 소유자명 사이의 정수)가 2 로 증가된 것이 보인다!<br><br>
이번에는 심볼릭 링크를 만들어보자.

```javascript
ln -s Gemfile Gemfile-sym

ls -li
4304662636 -rw-r--r--   2 sangwoo  staff  1110  9 27 22:06 Gemfile
4304662636 -rw-r--r--   2 sangwoo  staff  1110  9 27 22:06 Gemfile-hard
4305186419 lrwxr-xr-x   1 sangwoo  staff     7 10  9 00:55 Gemfile-sym -> Gemfile
4304662645 -rw-r--r--   1 sangwoo  staff  6803  9 27 22:07 Gemfile.lock
4304662633 -rw-r--r--   1 sangwoo  staff  2179  9 29 01:32 _config.yml
```

새로 생긴 심볼릭 링크는 새로운 inode 를 생성해서 가리키기에 inode 넘버도 다르고, 기존 링크들의 inode 참조수도 변하지 않음을 알 수 있다.<br>
그렇다면 여기서 기존 파일을 삭제하면 어떻게 될까?

```javascript
rm Gemfile

ls -li
4304662636 -rw-r--r--   2 sangwoo  staff  1110  9 27 22:06 Gemfile-hard
4305186419 lrwxr-xr-x   1 sangwoo  staff     7 10  9 00:55 Gemfile-sym -> Gemfile
4304662645 -rw-r--r--   1 sangwoo  staff  6803  9 27 22:07 Gemfile.lock
4304662633 -rw-r--r--   1 sangwoo  staff  2179  9 29 01:32 _config.yml
```

하드 링크에는 아무런 변화가 없지만, 운영체제와 터미널 설정에 따라 심볼릭 링크는 깨진 링크라는 표시를 해주기도 한다. 실제로 링크에 접근해 보면, 하드링크는 아무 문제없이 접근할 수 있지만 심볼릭 링크는 존재하지 않는 경로라는 메세지를 보여준다.

<img src="/assets/images/linux-link2.png">
