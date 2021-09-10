**도커 컨테이너 기본 명령어**

Ubuntu
``` Powershell
docker search --limit 5 wordpress # 도커 이미지 워드프레스 5개만 검색(인기순); 걍 사이트 가서 찾..
docker search --filter stars=50 wordpress # 50개 이상의 좋아요를 받은 것만 검색


uname -r # 우분투 커널 버전 확인
sudo apt-get update # 업데이트 도커엔진
docker ps # 현재 프로세스가 돌아가는 것을 조회


sudo service docker start # 도커 시작
sudo docker pull [images-name]
sudo docker images # 이미지 목록 조회
sudo docker inspect [image-id] # 상세 정보 조회
sudo docker image history [images-name] # 로그정보 조회
sudo docker container stop [container id or name]


sudo docker container run -i -t --name [이름지정] [images-name] /bin/bash # 컨테이너안으로 진입(루트디렉토리)
sudo apt-get update # 컨테이너 안에서 실행하면 그 안에서만 영향이 미치는 것임
sudo du -sh /var/lib/docker/overlay2/ # 저장된 정보 용량 확인
sudo docker images -a # 컨테이너 안에서(?)하면 관련된 이미지들이 모두 나오게 된다. 만약 불필요한 이미지가 있다면,
sudo docker rmi -f $(docker images -a -q) # 이미지 전부 삭제, 거부나면 sudo su - 관리자 권한으로 들어가서 실행
```

Powershell
``` Powershell
wsl.exe --shutdown Ubuntu # Ubuntu WSL 인스턴스를 종료
```