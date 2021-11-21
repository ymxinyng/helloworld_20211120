**getopt**


shellscript로 프로그램을 만들다 보면 실행 인자 및 옵션을 필요로 하는 경우가 많습니다.\
shellscript에서 실행 옵션을 구현하는 방법은 getopt함수를 쓰면 매우 간단해집니다.

+ **간단한 실행 옵션 예제**
  + 인자가 있는 실행옵션

    옵션뒤에 ":"를 붙여준다.\
    아래예에서는 "m"옵션과 "u"옵션에 해당합니다.
   
   
   + 인자가 없는 실행옵션
   
       옵션만 정의합니다. 아래 예에서는 "f","q","v","d","g","h"옵션이 해당합니다.
```python
#!/bin/sh
usage()
{
        echo "$0 -m MODE[,MODE,...] [-u USER -f -q -v -d -g -h]"
        echo "<options>"
        echo "    -m MODE[,MODE,...]:" 
        echo "           : MODE is 'GET' or 'SET' or 'SHOW'" 
        echo "             If MODE is GET, get Info."
        echo "             If MODE is SET, set Info."
        echo "             If MODE is SHOW, show Info"
        echo "    -u USER: user name usging logging. default is 'unknown'"
        echo "    -f     : Without waiting for a lock, force the update."
        echo "    -q     : Optional. Quiet mode"
        echo "    -v     : Optional. Verbose mode"
        echo "    -d     : Optional. print debug message"
        echo "    -g     : Optional. No logging to syslog"
        echo "    -h     : Show this message."
        echo "<exit code>"
        echo "    0: Success."
        echo "    1: Failure."
        exit 1
}

#실행 옵션 설정
while getopts m:u:fqvdgh opts; do
        case $opts in
        m) O_MODES=$OPTARG
                ;;
        u) O_USER=$OPTARG
                ;;
        f) O_FORCE="yes"
                ;;
        q) O_QUIET="yes"
                ;;
        v) O_VERBOSE="yes"
                ;;
        d) O_DEBUG="yes"
                ;;
        g) O_SYSLOG="no"
                ;;
        h) usage
                ;;
        \?) usage
                ;;
        esac
done

#실행 옵션 확인
echo "O_MODES=${O_MODES}"
echo "O_USER=${O_USER}"
echo "O_FORCE=${O_FORCE}"
echo "O_QUIET=${O_QUIET}"
echo "O_VERBOSE=${O_VERBOSE}"
echo "O_DEBUG=${O_DEBUG}"
echo "O_SYSLOG=${O_SYSLOG}"
```

**사용방법 확인**



"-h 옵션"을 이용하여 사용법을 확인합니다.\
`
getopt_test.sh -h
`
 
<img src ="https://user-images.githubusercontent.com/94737280/142752172-e4a02331-5332-4bf0-8d69-e96ef978e55f.png" width ="60%" height="60%" >



**인자가 없는 옵션**
```
getopt_test.sh -f -q -v -d -g
getopt_test.sh -fqvdg
getopt_test.sh -m GET -u leo -fqvdg
```


<img src = "https://user-images.githubusercontent.com/94737280/142752355-5ccf3259-0578-4efe-8724-41c061ff748d.png" width ="60%" height="60%" >

---


**getopts**

**용도**


명령행 인수를 처리하고 유효한 옵션을 검사합니다.

**구문**


`
getopts OptionString Name [ Argument ...]
`


**설명**


getopts 명령은 매개변수 리스트에서 옵션 및 옵션 인수를 검색하는 Korn/POSIX 쉘 내장 명령입니다. 옵션은 +(더하기 부호) 또는 -(빼기 부호)로 시작하고 그 뒤에 문자가 옵니다. + 또는 -로 시작하지 않는 옵션은 OptionString을 종료합니다. getopts 명령은 호출될 때마다 Name의 다음 옵션 값과 쉘 변수 OPTIND에서 처리될 다음 인수의 색인을 배치합니다. 쉘이 호출될 때마다 OPTIND는 1로 초기화됩니다. 옵션이 +로 시작되는 경우 +는 Name의 값 앞에 추가됩니다.


*OptionString*의 문자 뒤에는 :(콜론)이 오면 옵션에 인수가 있는 것으로 간주됩니다. 옵션에 옵션-인수가 필요한 경우 getopts 명령은 이를 변수 OPTARG에 배치합니다.


*OptionString*에 포함되지 않은 옵션 문자가 발견되거나 찾은 옵션에 필요한 옵션-인수가 없는 경우:

+ OptionString이 :(콜론)으로 시작되지 않는 경우
    + Name은 ?(물음표) 문자로 설정됩니다.
    + OPTARG가 설정되지 않으며
    + 진단 메시지가 표준 오류에 기록됩니다.


이 조건은 **getopts** 명령을 처리하는 중에 생긴 오류가 아니라 호출 애플리케이션에 인수가 표시되는 중에 발견된 오류라고 간주됩니다. 진단 메시지는 명시된 대로 기록되지만 종료 상태는 0이 됩니다.

 + *OptionString*이 :(콜론)으로 시작되는 경우
    + Name은 ?(물음표)로 설정되거나 누락된 필수 옵션에 대해서는 :(콜론) 문자로 설정됩니다.
    + OPTARG는 발견된 옵션 문자로 설정되고
    + 출력이 표준 오류에 기록되지 않습니다.


다음 중 하나가 옵션의 끝을 식별합니다. 특수 옵션 - -,이 - 또는 +로 시작되지 않는 인수를 찾는 경우 또는 오류가 발생하는 경우.

+ 옵션의 끝에 도달하는 경우:

  + getopts 명령은 0보다 큰 리턴 값으로 종료됩니다.
  + OPTARG는 첫 번째 비옵션-인수의 색인으로 설정됩니다. 여기서, 첫 번째 - - 인수는 그 전에 다른 비옵션-인수가 나타나지 않는 경우 옵션-인수로 간주되고 비옵션-인수가 없는 경우에는 값 $#+1로 간주됩니다.
  + Name은 ?(물음표) 문자로 설정됩니다.
 
 **매개변수**
 
 
|항목|설명|
|---|-----|
|optionstring|**getopts** 명령이 인식하는 옵션 문자의 문자열을 포함합니다. 문자 뒤에 콜론이 오는 경우 해당 옵션은 인수가 있는 것으로 간주되며 이 인수는 별도의 인수로 제공되어야 합니다. 공백을 사용하여 옵션을 인수와 분리할 수 있습니다. OptionString의 첫 번째 문자는 옵션 문자를 알 수 없거나 옵션-인수가 누락된 경우 getopts 명령이 작동하는 방식을 판별합니다.|
|이름|getopts 명령에 의해 발견된 옵션 문자로 설정됩니다.|
|Argument...|공백으로 분리된 하나 이상의 문자열이며 올바른 옵션인지 **getopts** 명령이 검사합니다. Argument가 생략되는 경우 위치 매개변수가 사용됩니다. .|
 
 
 **종료 상태**
 
 이 명령은 다음과 같은 종료값을 리턴합니다.
 |항목|설명|
 |---|---|
 |o|OptionString에 의해 지정되거나 지정되지 않은 옵션이 발견되었습니다.|
 |>o|옵션의 끝에 도달했거나 오류가 발생했습니다.|
 
 **예제**
 1. 다음 getopts 명령은 a, b 및 c가 유효한 옵션이고 a 및 c 옵션에 인수가 있음을 지정합니다


 `
 getopts a:bc: OPT
 `
 
 
 2. 다음 getopts 명령은 a, b 및 c가 유효한 옵션이고 a 및 b 옵션에는 인수가 있으며 명령행에서 정의되지 않은 옵션을 발견하는 경우 getopts가 OPT의 값을 ?로 설정하도록 지정합니다


 `
 getopts :a:b:c OPT
 `
 
 
 3. 다음 스크립트가 인수를 구문 분석하고 표시합니다


 ```phython
 aflag=
bflag=
 
while getopts ab: name
do
            case $name in
            a)     aflag=1;;
            b)     bflag=1
                          bval="$OPTARG";;
            ?)     printf "Usage: %s: [-a] [-b value] args\n" $0
                          exit 2;;
           esac
done
 
if [ ! -z "$aflag" ]; then
           printf "Option -a specified\n"
fi
 
if [ ! -z "$bflag" ]; then
           printf 'Option -b "%s" specified\n' "$bval"
fi
 
shift $(($OPTIND -1))
printf "Remaining arguments are: %s\n" "$*"
```
 
---

**awk**

**awk 명령어란?**
awk 란 패턴 탐색과 처리를 위한 명령어로 간단하게 파일에서 결과를 추려내고 가공하여 원하는 결과물을 만들어내는 유틸리티입니다.

즉 파일에서 패턴이 일치하는 행을 찾아서 지정한 조치를 수행해주는 명령어이다. awk 명령은 사용자가 정의한 명령어 집합을 이용하여 파일 집합과 사용자가 제공한 확장 표현식을 한번에 한 행씩 비교한 다음 확장 정규식과 일치하는 모든 행에 작용하여 특별한 작업을 해 준다.

초기 개발자 Aho, Weinberger, Kernighan의 첫글자를 따서 이름지어진 awk는 GNU 프로젝트에서 만들어진 텍스트 처리 프로그래밍 언어로 유닉스 계열의 OS에서 사용 가능하며, 텍스트 형태로 되어있는 입력 데이터를 행과 단어 별로 처리해 출력한다.

**awk 기본명령어**

```python
awk [OPTION...] [awk program] [ARGUMENT...]
      OPTION
        -F        : 필드 구분 문자 지정.
        -f        : awk program 파일 경로 지정.
        -v        : awk program에서 사용될 특정 variable값 지정.
      awk program
        -f 옵션이 사용되지 않은 경우, awk가 실행할 awk program 코드 지정.
      ARGUMENT
        입력 파일 지정 또는 variable 값 지정.
```

awk 명령을 통해 주로 하는 명령은 다음과 같습니다.


+ 텍스트 파일의 전체 내용 출력.
+ 파일의 특정 필드만 출력.
+ 특정 필드에 문자열을 추가해서 출력.
+ 패턴이 포함된 레코드 출력.
+ 특정 필드에 연산 수행 결과 출력.
+ 필드 값 비교에 따라 레코드 출력.

**awk 기본구조**

`
pattern {action}
`

**awk program**

`
awk [OPTION...] 'pattern { action }' [ARGUMENT...]
`

 awk 명령에서 awk program은 ‘ ‘(single quotation marks) 안에 작성합니다.

아래와 같이 pattern이 생략되는 경우, 매칭 여부를 검사할 문자열 패턴 정보가 없기 때문에 모든 레코드가 선택되고, action을 생략하면, 기본 액션인 print가 실행되는 것입니다.

```python
# pattern 생략.
$ awk '{ print }' ./file.txt      # file.txt의 모든 레코드 출력.
 
# action 생략.
$ awk '/p/' ./file.txt            # file.txt에서 p를 포함하는 레코드 출력.
 
```

**awk record 적용방식**

pattern과 action에 작성되는 awk program 코드에는 다양한 표현식, 변수, 함수 등이 사용됩니다. 이 중 가장 중요한 변수는 레코드와 필드를 나타내는 변수인데, 하나의 레코드는 $0, 레코드에 포함된 각 필드는 그 순서대로 $1, $2, …, $n 으로 지칭됩니다.


![awk](https://user-images.githubusercontent.com/94737280/142753292-2a617e20-50a5-40d3-ac00-9c2d9613b1ba.png)


**awk 사용예제**

|awk 사용 예|명령어 옵션|
|---|---|
|파일의 전체 내용 출력|awk ‘{ print }’ [FILE]|
|필드 값 출력|	awk ‘{ print $1 }’ [FILE]|
|필드 값에 임의 문자열을 같이 출력|	awk ‘{print “STR”$1, “STR”$2}’ [FILE]|
|지정된 문자열을 포함하는 레코드만 출력|	awk ‘/STR/’ [FILE]|
|특정 필드 값 비교를 통해 선택된 레코드만 출력|	awk ‘$1 == 10 { print $2 }’ [FILE]|
|특정 필드들의 합 구하기|	awk ‘{sum += $3} END { print sum }’ [FILE]|
|여러 필드들의 합 구하기|	awk ‘{ for (i=2; i<=NF; i++) total += $i }; END { print "TOTAL : "total }' [FILE]|
|레코드 단위로 필드 합 및 평균 값 구하기|	awk ‘{ sum = 0 } {sum += ($3+$4+$5) } { print $0, sum, sum/3 }’ [FILE]|
|필드에 연산을 수행한 결과 출력하기|	awk ‘{print $1, $2, $3+2, $4, $5}’ [FILE]|
|레코드 또는 필드의 문자열 길이 검사	|awk ‘ length($0) > 20’ [FILE]|
|파일에 저장된 awk program 실행|	awk -f [AWK FILE] [FILE]|
|필드 구분 문자 변경하기	|awk -F ‘:’ ‘{ print $1 }’ [FILE]|
|awk 실행 결과 레코드 정렬하기	|awk ‘{ print $0 }’ [FILE]|
|특정 레코드만 출력하기	|awk ‘NR == 2 { print $0; exit }’ [FILE]|
|출력 필드 너비 지정하기	|awk ‘{ printf “%-3s %-8s %-4s %-4s %-4s\n”, $1, $2, $3, $4, $5}’ [FILE]|
|필드 중 최대 값 출력	|awk ‘{max = 0; for (i=3; i max) ? $i : max ; print max}’ [FILE]|



