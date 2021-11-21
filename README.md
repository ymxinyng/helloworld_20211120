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
