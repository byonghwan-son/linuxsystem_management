# linuxsystem_management
## 명령어 History 검색
* **역방 검색**
  * ctrl + R을 눌러 검색할 이전 명령어를 입력
  * ctrl + R을 반복해서 누르면 입력한 명령어가 입력된 것만 추출해 줌.
* **전방 검색**
  * vim ~/.bashrc
  * shift + G로 마지막 행으로 이동
  * stty stop undef

## 원격지 파일 복사
* SCP (Secure CoPy)
  > $ scp ./file.txt username@xxx.xxx.xxx.xxx:/tmp
  
  > $ scp username@xxx.xxx.xxx.xxx:/tmp/file.txt ~/
* 옵션
  * -r : 서브 디렉토리까지 모두

## 시스템 과부하를 파악하기
* top
  * 부하 지표 : 오른쪽 상단의 load average를 파악하자
    * CPU가 처리하는 걸 기다리는 작업 갯수
    * 1분당 평균으로 몇 개의 일이 쌓이는지를 나타내는 값
    * 코어 수보다 많다면 대기가 되고 있다는 의미
    * load average : 0.00 (1분간 평균) 0.00 (5분간 평균) 0.00 (15분간 평균)
  * TIME+
    * 프로세스의 CPU 시간 합계가 "분:초.(1/10초)" 형식으로 나옴
    * 실제로 CPU를 사용한 시간
    * 과부하 원인을 찾을 때는 "CPU 사용률이 높다", "CPU 시간도 길다".
  * COMMAND
    * 자세한 명령어는 "C"를 누르면 자세히 표시된다.
  * 과부하 명령어는 과감하게 kill
    > kill -9 PID
  * 메모리 부족
    * swap 발생량 확인하기
    * %MEM : 프로세스가 소비하는 메모리량    
  * 표시 정렬
    * shift + M : 메모리 사용량 순서로 나열
    * shift + T : CPU 시간 순서
    * shift + P : CPU 사용량 순서

## 로그파일에서 필요한 줄만 뽑고 싶어 (파이프 라인)
> grep -r "찾을 단어" "경로"
* 파이프( | ) 이용하기
> grep "찾을 단어" "파일 명" | less
* grep을 여러 개 붙여서 사용하는 것도 가능 (-v : 제외하고)
> grep -r "찾을 단어" "경로" | grep -v "찾을 단어" | less
* tail 사용한 파이프
> tail -F access.log | grep "/retro" | grep -v "/live"
* 파이프 라인을 사용하면 명령어끼리 조합해서 사용 가능
* grep은 다른 명령어 출력을 가공하는 데도 사용 가능
* zcat을 사용하면 압축된 로그 파일에서 바로 파이프라인으로 연결 가능

## 작업 절차를 자동화하고 싶어 (쉘 스크립트)
* 셔뱅(shebang) : 스크립트를 실행하는 프로그램(인터프린터)을 지정. Hash-Bang이라고도 함.
```bash
#!/bin/bash
```
* 그외 셔뱅 언어들.
  * bash, zsh, perl, python, node, ruby, ...
* 작성 완료 후 실행권한 부여
  * chmod +x setup.sh
  * ./setup.sh => 항상 ./ 를 쓰지 않고 실행하려면 .bashrc에서 "." 을 path에 추가
* 명령에 이상이 있을 때 종료하기
```bash
# $? : 직전에 실행한 명령어의 결과 값 ( 0 : 성공, 그외에는 에러 값)
if [ $? != 0 ]: then exit: fi
```

## 같은 문자열을 스크립트에서 재사용하고 싶어(쉘 변수)
* vim에서 문자 치환하기
```
 :%s/원문/수정문/
 # s는 substitude
```
* 변수 사용하기
  * 변수 지정 : ```변수명=값```
  * 변수 사용 : ```$변수명``` 혹은 ```{$변수명}```

## 작업 환경과 상태를 정해서 스크립트를 실행하고 싶어 (환경변수)
* ```$HOME``` : 로그인 사용자의 home 디렉토리
* 환경 변수 확인하기
> env
* 대표적인 환경변수
  * HOME : 현재 사용자의 홈 디렉터리 경로
  * PWD : 현대 디렉터리(작업 디렉터리) 경로
  * EDITOR : 정해진 텍스트 에디터(Vim, Emacs, nano 등) 경로
  * PAGER : 정해진 페이지(less, lv 등) 경로
  * USER : 현재 사용자의 사용자명
  * GROUP : 현재 사용자의 그룹명
  * HOSTNAME : 머신의 호스트명
* 날짜 변형
  > echo $(date +%Y-%m-%d) -> 2023-09-25
