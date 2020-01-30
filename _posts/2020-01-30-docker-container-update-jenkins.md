---
layout: archive
title:  "New Jenkins Container And Update Jenkins (Docker)"
date:   2020-01-30 00:00:00 +0900
---

![docker_jenkins_logo](/assets/images/2020-01-30-docker-container-update-jenkins/01.png)

# 젠킨스 컨테이너 생성

1. 젠킨스 볼륨 생성 (호스트 서버)

```bash
# I'll keep it as "jenkins", you can use "jenkins_home" if you wish

mkdir -p $HOME/jenkins
```

2. 젠킨스 이미지 실행

```bash
# if you're using other names, replace $HOME/jenkins with your choice
# in case you want to change port, use this
# -e JENKINS_OPTS="--httpPort=80" (remember to change 8080:8080 below to 80:80)

docker container run \
--name jenkins \
-p 8080:8080 -p 50000:50000 \
-v $HOME/jenkins:/var/jenkins_home \
-d \
jenkins
```

3. 화면에 보이는 비밀번호 확인

4. http://YOUR-SERVER-IP:8080 접속

---

# 젠킨스 버전 업데이트

1. 다운로드 링크를 오른쪽 클릭하고 "복사" 를 누른다.

![copy_link_address](/assets/images/2020-01-30-docker-container-update-jenkins/02.png)

2. 젠킨스 컨테이너 로그인

```bash
#using host's root with -u 0

docker container exec -u 0 -it jenkins bash
```

3. 1. 에서 복사 한 링크 파일 다운로드

```bash
# inside the container, using 2.89.2 as example
wget http://updates.jenkins-ci.org/download/war/2.204.2/jenkins.war
```

4. 다운로드 파일 이동

```bash
mv ./jenkins.war /usr/share/jenkins
```

5. 권한 조정

```bash
chown jenkins:jenkins /usr/share/jenkins/jenkins.war
```
6. 컨테이너 나가기 및 재시작

```bash
# exit contaienr (inside container)
exit
# restart container (from your server)
docker container restart jenkins
```

## In case if you see a deprecated protocols: JNLP-connect, JNLP2-connect warning 노출시 해결 방안

![check_deprecated protocols_01](/assets/images/2020-01-30-docker-container-update-jenkins/03.png)

![check_deprecated protocols_02](/assets/images/2020-01-30-docker-container-update-jenkins/04.png)
