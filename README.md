### [참고 : 길벗 : 만화로 배우는 리눅스 시스템 관리 1](https://www.yes24.com/Product/Goods/30705473)
---
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

## 로그 파일에서 필요한 줄만 뽑고 싶어
* cut 명령어 이용
> cat access.log | grep -v 'jpg' | cut -d ' ' -f 7 | less
* 명령어 설명
  * grep : jpg 문자는 제외
  * cut : 구분자(delimeter) 는 스페이스(' ') 이고 구분했을 때 7번째 값만 추출
> cat access.log | grep -v 'jpg' | cut -d '[' -f 2 | cut -d ']' -f 1
* 명령어 설명
  * grep : jpg 문자는 제외
  * cut : [] 사이에 있는 접속 시간을 추출
> echo $PWD | cut -d "/" -f 2
* 명령어 설명
  * 현재 경로에서 root로 부터 1 번째 디렉토리 이름만 추출
  * 1: / 2:var / 3:log / 4:apache2
> cat access.log | grep -v 'jpg' | cut -d ' ' -f 7 | sort | uniq -c | sort -r | less
* 명령어 설명
  * 접속 경로를 정렬한 후에 중복을 제거한 다음 해당 행의 갯수를 표시한 결과에 다시 정렬을 해서 가장 접속을 많이 한 페이지부터 표시한다.
> cat access.log | grep -v 'jpg' | cut -d ' ' -f 7 | sort | uniq -c | sort -r | less | tail -n 10
* 명령어 설명
  * 위의 설명에 마지막 10 줄만 표시
> cat access.log | grep -v 'jpg' | cut -d ' ' -f 7 | sort | uniq -c | sort -r | less | head -n 10
* 명령어 설명
  * 위의 설명에 첫번째 10 줄만 표시

## CSV 파일을 열의 내용에 따라 정렬하고 싶어
* 주문 대비 출고 현황에서 단가가 비싼 순서대로 출력하기
> cat 0914주문대비출고현황.csv | cut -d ',' -f 8-10,14,15 | sort -t ',' -k 4 -n -r -b >  output.csv
* 명령어 설명
  * cut : csv 파일을 컴마로 분리해서 8~10번째, 14번째, 15번째 행을 추출
  * sort : cut으로 추출한 내용을 다시 컴마로 분리(-t ',')해서 4번째 행(-k 4)을 정렬에 사용한다
  * sort는 문자열을 비교해서 정렬을 하기 때문에 정렬하고자 하는 행이 숫자인 경우는 -n 혹은 --number를 지정해서 숫자로 재해석 후 다시 정렬을 한다.
  * 문자열의 시작에 스페이스가 들어가 있을 경우 빈문자열로 인해 원하는 정렬이 안된 경우 -b or --ignore-leading-blanks로 빈 문자열을 무시하게 한다.

## 명령줄 지정으로 작업 내용을 바꾸고 싶어 (명령줄 인수)
```bash
#!/bin/bash
echo $1 $2
# 파일명은 echo_param.sh
```
* 실행 가능으로 변경
> chmod +x echo_param.sh
* 파라미터 적용하기
> ./echo_param.sh file1 file2
---
**결과** : file1 file2

* 옵션과 파라미터의 연결
```bash
#!/bin/bash

while getopts b:n:p:o: OPT
do
  case $OPT in
    b) base="$OPTARG" ;;
    n) next="$OPTARG" ;;
    p) previous="$OPTARG" ;;
    o) output="$OPTARG" ;;
  esac
done

echo base=$base next=$next previous=$previous output=$output
# 파일명은 option.sh
```
> ./option.sh -b aaa -n bbb -p ccc -o ddd
---
**결과** : base=aaa next=bbb previous=ccc output=ddd

## 조건에 따라 처리 흐름을 바꾸고 싶어 (조건 분기)
```bash
#!/bin/bash

if [ $# = 2 ]
then
  echo '인수가 2개'
else
  echo '인수가 2개가 아니야'
fi

```

## 명령어 이상 종료에 대응하고 싶어(종료 상태)
* ```$?```
  * 0 : 명령 성공
  * 그 외 : 명령 실패
* ```$?```으로 직전에 실행한 명령어 종료 상태를 참조 가능
* ```$?``` 값은 명령어가 정상 종료하면 0, 이상 종료하면 0 이외의 값이 됨
* ```exit```에 인수로 숫자를 지정하면 셀 스크립트의 종료 상태가 됨
* ```if```로 종료 상태를 참조하면 명령어가 정상 종료했는지에 따라 조건 분기가 가능

※ 종료 상태는 0~255까지가 범위

## 같은 처리를 반복해서 실행하고 싶어(for)
```bash
#!/bin/bash

for filename in cd /var/log/apache2; ls *.log | grep -v 'error.log'
do
  ./create-report.sh $filename
done

```
* /var/log/apache2 디렉터리에 있는 확장자가 log인 파일 가운데 [error.log]만 제외(-v)하고 모두 가져와서 하나씩 filename 변수에 담아서 실행한다.

## 공통 처리를 계속 재사용하고 싶어(쉘 함수)
```bash
#!/bin/bash

do_copy() {
  cp /tmp/source $1
}

do_copy()

# copy_files.sh
```
> ./copy_files.sh /tmp/target

위와 같이 작성하면 오류가 발생한다.
```
cp: missing destination file operand after '/tmp/source'
Try 'cp --help' for more information
```
$1이 스크립트의 파라미터 인지 함수의 파라미터 인지 애매하기 때문이다. \
아래과 같이 작성을 해야 한다.
```bash
#!/bin/bash

target=$1

do_copy() {
  cp /tmp/source $target
}

do_copy()

# copy_files.sh
```
