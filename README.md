# **getopt**


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


# **getopts**



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


# **sed**


**sed란 무엇인가?**

 

sed는 비 대화형 모드의 줄 단위 편집기라고 합니다. 

vi 편집기처럼 직접 파일을 열어 고치지 않고 커맨드 창 또는 스크립트에서 동작을 하여 원하는 부분만 변경해준다는 특징이 있습니다.


*동작원리*



<img src="https://user-images.githubusercontent.com/94737280/142754266-f4d413bb-b151-45f7-b1d1-87d017b16177.png" width="30%" height="30%">



**sed의 특징**


sed는 쉘 또는 스크립트에서 파이프(|)와 같이 사용될 수 있는 명령어입니다.

그리고 기본적으로 정규표현식이 사용가능합니다.

주의할 점은 정규표현식을 사용하기 때문에 특수문자 앞에 역 슬래시(\,＼)를 붙여주어야 합니다. \를 쓰면 리눅스에서는 역슬래시로 받아들입니다.

예) 
`
sed 's/\$man/man/g' test.txt
`



**sed 옵션**

|옵션|설명|
|---|---|
|e| sed를 사용하였을 때 출력되는 값을 보여줍니다. 이 옵션은 기본값으로 굳이 안 써도 되지만 다중 명령어를 쓸 때는 반드시 써야 합니다. 예) sed /man/p -e /girl/p 파일명: man과 girl이 들어있는 줄을 한번 더 출력|
|n|특정 값이 들어간 줄만 출력해주는 기능입니다. 주로 p 명령어와 사용된다고 합니다.|
|f|스크립트를 파일로부터 읽어들이며 명령어를 지정하는 명령어라고 하는데 거의 사용하지 않습니다.|


**sed 명령어**


|명령어|설명|
|---|---|
|a\ |해당 값이 있는 줄 다음에 입력|
|i\ |줄 앞에 첨가 명령어|
|c\ |해당 줄을 변경하는 명령어|
|g|한 줄에 해당하는 값이 여러개 있을 경우에 모두 변경하는 명령어. 이 명령어를 해당 줄의 제일 앞에 내용만 변경됩니다.|
|p|조건에 부합하는 라인을 출력하는 명령어|
|s/orgin/change/|앞에 것을 뒤에 것으로 변경하는 명령어|


**유용한 sed 이용법**



- 공백줄제거하기

`
sed '/^$/d' 파일명
`

- 모든 줄마다 공백을 추가하기

`
sed 'a\\' 파일명
`

- 주석이 처리된 줄 모두삭제

`
sed '/^#/d' 파일명
`



**예제를 이용한 sed사용법**



sed_test_file.txt


```txt
name    phone           birth           sex     score
reakwon 010-1234-1234   1981-01-01      M       100
sim     010-4321-4321   1999-09-09      F       88
nara    010-1010-2020   1993-12-12      M       20
yut     010-2323-2323   1988-10-10      F       59
kim     010-1234-4321   1977-07-17      M       69
nam     010-4321-7890   1996-06-20      M       75
sol     010-5911-1111   1976-10-12      F       60
lee     010-4949-4949   1988-09-30      F       80
feng    010-1111-9999   1979-03-20      M       90
```

기본적으로 sed는 아래와 같은 형태로 사용합니다. 

`
sed -n -e 'command' [input file]
`

**-n**:ed는 pattern buffer의 내용을 자동적으로 출력해주는데, 이 옵션을 사용하게 되면 자동 출력을 하지 않습니다. -n옵션을 하지 않고 sed명령을 해보시면 눈에 띄게 많은 데이터가 출력되는것을 확인할 수 있습니다. 그래서 -n옵션을 붙여 패턴 버퍼의 자동출력을 하지 않습니다. 여러분은 그래서 sed 명령의 기본 형태는 sed -n으로 생각하시면 됩니다.


**-e**:이 옵션 다음으로는 우리가 사용할 command를 가지고 텍스트 파일을 가공해줍니다. -e의 command에는 어떤 종류가 있는지 확인해보도록 합시다.
그리고 맨 마지막에는 가공할 원본 파일을 지정해주면 됩니다. 




**특정 행을 출력 - p (print)**



- 전체 내용을 출력 : 기본적으로 전체의 줄을 출력하려면 command에 /$/p 또는 1,$p로 출력해볼 수 있습니다. 

```python
sed -n -e '1,$p' sed_test_file.txt
sed -n -e '/$/p' sed_test_file.txt
```


- 첫번째 줄 출력 : 첫번째 줄만 출력해주기를 아래의 command를 사용할 수 있습니다. 


`
sed -n -e '1p' ./sed_test_file.txt
`



`
name    phone           birth           sex     score
`

- start ~ end 줄까지 출력 : 그렇다면 첫째줄부터 4번째줄을 출력하기를 원한다면 쉼표로 구분된 출력하길 원하는 줄, 마지막 줄 p 의 형태를 사용하면 됩니다.

`
sed -n -e '1,4p' sed_test_file.txt
`


```
name    phone           birth           sex     score
reakwon 010-1234-1234   1981-01-01      M       100
sim     010-4321-4321   1999-09-09      F       88
nara    010-1010-2020   1993-12-12      M       20
```


- 특정줄에서 마지막 줄까지 출력 : 어떤 줄에서 마지막 줄까지 출력하려면 $문자를 사용하면 됩니다. $는 마지막줄을 의미합니다. 그래서 5번째 줄에서 마지막줄까지 출력하려면 아래와 같은 command를 사용할 수 있습니다.

`
sed -n -e '7,$p' sed_test_file.txt
`


```
nam     010-4321-7890   1996-06-20      M       75
sol     010-5911-1111   1976-10-12      F       60
lee     010-4949-4949   1988-09-30      F       80
feng    010-1111-9999   1979-03-20      M       90
```

- 다중 command 사용 : 만약 command를 여러개 사용하고 싶다면 -e 옵션을 이용해서 여러개 사용하여 command를 줄 수 있습니다. 만약 1번째 줄을 출력해주고, 8번째 줄부터 끝까지 출력하기를 원한다면 아래의 명령을 사용하면 됩니다.

`
sed -n -e '1p' -e '8,$p'  sed_test_file.txt
`

```
name    phone           birth           sex     score
sol     010-5911-1111   1976-10-12      F       60
lee     010-4949-4949   1988-09-30      F       80
feng    010-1111-9999   1979-03-20      M       90
```


**특정 문자열이 있는 줄 출력**


만약 위의 파일에서 여자(F, Female)만 출력한다고 하다면 어떻게 command를 어떻게 사용할까요? 아래와 같은 형식대로 사용하면 됩니다.

`
/포함된 문자열/p
`



**특정 행 삭제 - d (delete)**


행삭제에 관한 명령어는 d를 사용하면 됩니다. d를 사용하는 것외에는 위의 줄 출력을 해주는 명령어 형태로 사용하면 됩니다. 

`
sed -n -e '2,6d' -e '1,$p'  sed_test_file.txt
`


```
name    phone           birth           sex     score
nam     010-4321-7890   1996-06-20      M       75
sol     010-5911-1111   1976-10-12      F       60
lee     010-4949-4949   1988-09-30      F       80
feng    010-1111-9999   1979-03-20      M       90
```



**단어 치환(Substitute) - s (substitute)**

특정 단어를 치환하려면 s명령을 이용하면 됩니다. 형식은 아래와 같습니다. 

- 기본적인 특정단어 치환


```
s/old/new/g
s/old/new/gi
```

단어 s는 substitute(치환)의 약자, 그리고 /로 구분하여 old는 단어를 치환할 문자열, new는 새롭게 치환한 문자열인데 비어있으면 그 문자열을 삭제한 효과를 가질 수 있습니다. g는 global의 약자로 전체에 적용됨을 의미합니다. 


두번째의 i는 ignore case의 약자로 old의 단어를 검색할때 대소문자 구분하지 않겠다는 것을 의미합니다. 

아래 명령의 결과를 보고 차이점을 확인해보세요.


```
sed -n -e 's/reakwon/reak/g' -e '2p' sed_test_file.txt
sed -n -e 's/reakwoN/reak/g' -e '2p' sed_test_file.txt
sed -n -e 's/reakwoN/reak/gi' -e '2p' sed_test_file.txt
```

- 특정 단어로 시작 혹은 끝나는 단어를 포함하는 라인의 문자열 치환

특정단어로 시작하는 단어로 문자열을 치환하는 것도 가능합니다. 특정 단어로 시작하는 줄을 선택하려면 앞에 '^'문자를 사용하면 됩니다. 사실 정규표현식을 이용할경우 앞에 ^가 붙는다면 ^이후에 나오는 문자열로 시작되는 문자열들을 추출합니다. 단 많이 실수하시는 부분이 특정 단어로 시작하거나 끝나는 문자열이 아닌 줄(line)입니다.

```
Let it go, let it go.
Can't hold it back anymore.
Let it go, let it go.
Turn away and slam the door.
I don't care what they're going to say.
Let the storm rage on.
The cold never bothered me anyway.
```

`
sed -n -e 's/^let/LET/gi' -e '1,$p' let_it_go.txt
`

반대로 끝나는 문자열은 끝에 $를 붙여줘서 검색하면 됩니다. Anyway. 으로 끝나는 줄의 Anyway를 대문자로 바꾸려면 아래와 같은 command를 사용하면 됩니다.


`
sed -n -e 's/anyway.$/ANYWAY/gi' -e '1,$p' let_it_go.txt
`



**문자열 추가 - a, i (append, insert)**


문자열을 추가하는 방법에는 두 가지 정도가 존재합니다. 해당 문자열 아래에 추가하느냐(Append) 아니면 이 전 줄에 삽입하느냐(Insert)가 있는데요. 기본적인 형식은 아래의 command처럼 사용합니다.

```
/찾을 문자열/a\다음 출에 추가할 문자열
/찾을 문자열/i\위에 삽입할 문자열
```



**특정 행의 내용을 전부 교체 - c (change)**

c command를 이용해서 행의 내용을 바꿀 수 있습니다. command 형태는 이렇습니다.

`
/바꿀 행이 포함한 문자열/c\바꿀 행의 내용
`
ex)Let으로 시작하는 줄의 내용을 바꾸고 싶다면 어떻게 할까요? 우선 ^를 사용하여 Let으로 시작하는 줄들을 찾고 c 커맨드로 바꿔질 줄 내용을 입력해주시면 됩니다.

`
sed -n -e '/^Let/c\Let it go X2' -e '1,$p' let_it_go.txt
`


**특정 행에 파일의 내용을 추가 - r (read)**


혹은 파일의 내용을 줄에다가 추가할 수도 있습니다. 여기 우리가 내용을 추가할 파일이 존재합니다.

perfect.txt

`
PERFECT! EXCELLENT!
`

100으로 끝나는 줄에 저 텍스트 파일의 내용을 아랫줄에 첨가하려면 아래와 같은 명령을 사용하면 됩니다.

`
 sed -n -e '/100$/r perfect.txt' -e '1,$p' sed_test_file.txt
`

```
name    phone           birth           sex     score
reakwon 010-1234-1234   1981-01-01      M       100
PERFECT! EXCELLENT!
sim     010-4321-4321   1999-09-09      F       88
nara    010-1010-2020   1993-12-12      M       20
yut     010-2323-2323   1988-10-10      F       59
kim     010-1234-4321   1977-07-17      M       69
nam     010-4321-7890   1996-06-20      M       75
sol     010-5911-1111   1976-10-12      F       60
lee     010-4949-4949   1988-09-30      F       80
feng    010-1111-9999   1979-03-20      M       90
```

---



# **awk**

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

**주요예제**


**특정 Record를 검색하기 - if 구문**

*~이상 , ~ 이하의 레코드 출력*


만약 위의 파일에서 점수가 80점 이상인 사람들의 레코드를 알고 싶다면 어떻게 하면 좋을까요? 이거는 pattern을 써야할까요, action을 써야할까요? action에서는 if 구문이 존재합니다. 그래서 이렇게 사용하면 80점 이상인 record를 출력할 수 있습니다.

`
awk '{ if ( $5 >= 80 ) print ($0) }' ./awk_test_file.txt
`

```python
name    phone           birth           sex     score
reakwon 010-1234-1234   1981-01-01      M       100
sim     010-4321-4321   1999-09-09      F       88
```

아래와 같은 구문으로도 사용할 수도 있습니다.

`
awk '$5 >= 80 { print $0 }' ./awk_test_file.txt
`

**내장함수**

awk에는 여러가지 내장함수들이 존재합니다. 단어의 길이를 알아내려면 length함수, 단어의 부분단어를 추출하려면 substr함수를 사용할 수 있습니다. 아래의 awk 명령은 이름의 길이와 이름의 3글자까지 출력을 하는 명령입니다.

`
 awk '{ print ("name leng : " length($1), "substr(0,3) : " substr($1,0,3)) }' ./awk_test_file.txt
 `
 
```python
name leng : 4 substr(0,3) : nam
name leng : 7 substr(0,3) : rea
name leng : 3 substr(0,3) : sim
name leng : 4 substr(0,3) : nar
name leng : 3 substr(0,3) : yut
name leng : 3 substr(0,3) : kim
name leng : 3 substr(0,3) : nam
 
```

**반복문**

```python
awk '{
for(i=0;i<2;i++)
 print( "for loop :" i "\t" $1, $2, $3)
}' ./awk_test_file.txt
```

```python
for loop :0     name phone birth
for loop :1     name phone birth
for loop :0     reakwon 010-1234-1234 1981-01-01
for loop :1     reakwon 010-1234-1234 1981-01-01
for loop :0     sim 010-4321-4321 1999-09-09
for loop :1     sim 010-4321-4321 1999-09-09
for loop :0     nara 010-1010-2020 1993-12-12
for loop :1     nara 010-1010-2020 1993-12-12
for loop :0     yut 010-2323-2323 1988-10-10
for loop :1     yut 010-2323-2323 1988-10-10
for loop :0     kim 010-1234-4321 1977-07-17
for loop :1     kim 010-1234-4321 1977-07-17
for loop :0     nam 010-4321-7890 1996-06-20
for loop :1     nam 010-4321-7890 1996-06-20
```

**BEGIN, END pattern**


BEGIN은 awk가 모든 레코드를 돌기 전에 한번 action을 수행하고 END는 모든 레코드를 다 돈 후에 마지막으로 정의한 action이 실행됩니다. 아래의 예에서 어떻게 사용되는지 살펴봅니다.


```python
 awk '
> BEGIN {
>  sum = 0
>  cnt = -1
> }
>
> {
>  sum += $5
>  cnt++
> }
>
> END {
>  avg = sum/cnt
>  print ("sum :" sum ", average :" avg)
> }' ./awk_test_file.txt
```

BEGIN에서는 초기화가 이루어지고, END에서는 결과를 출력해줍니다.
