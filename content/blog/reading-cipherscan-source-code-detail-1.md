+++
date = "2017-05-03T13:59:51+08:00"
description = ""
title = "cipherscan部分代码细节解释"

+++


## 代码段1
- 要求Bash版本4

```bash
1 if [[ ${BASH_VERSINFO[0]} -lt 4 ]]; then
2     echo "Bash version 4 is required to run cipherscan." 1>&2
3     echo "Please upgrade your version of bash (ex: brew install bash)." 1>&2
4     exit 1
5 fi
```

## 代码段2
- 检查环境中是否定义了`NOAUTODETECT`、`TIMEOUTBIN`和`OPENSSLBIN`。如果没有则使用`which`得到相应值。
- 使用GNU[`coreutils`](https://www.gnu.org/software/coreutils/coreutils.html)中的`greadlink`和`gtimeout`
	- [`gtimeout`](https://www.gnu.org/software/coreutils/manual/html_node/timeout-invocation.html): Run a command with a time limit
	- [`greadlink`](https://www.gnu.org/software/coreutils/manual/html_node/readlink-invocation.html): Print value of a symlink or canonical file name

```bash
if [[ -n $NOAUTODETECT ]]; the
    # -f 判断文件是否存在
    # -x 判断文件是否存在且可执行
    if ! [[ -f $TIMEOUTBIN && -x $TIMEOUTBIN ]]; then
        echo "NOAUTODETECT set, but TIMEOUTBIN is not an executable file" 1>&2
        exit 1
    fi
    if ! [[ -f $OPENSSLBIN && -x $OPENSSLBIN ]]; then
        echo "NOAUTODETECT set, but OPENSSLBIN is not an executable file" 1>&2
        exit 1
    fi
else
    case "$(uname -s)" in
        Darwin)
            opensslbin_name="openssl-darwin64"

            READLINKBIN=$(which greadlink 2>/dev/null)
            # -z 判断字符串是否为空，为空返回true
            if [[ -z $READLINKBIN ]]; then
                echo "greadlink not found. (try: brew install coreutils)" 1>&2
                exit 1
            fi
            TIMEOUTBIN=$(which gtimeout 2>/dev/null)
            if [[ -z $TIMEOUTBIN ]]; then
                echo "gtimeout not found. (try: brew install coreutils)" 1>&2
                exit 1
            fi
            ;;
        *)
            opensslbin_name="openssl"

            # test that readlink or greadlink (darwin) are present
            READLINKBIN="$(which readlink)"

            if [[ -z $READLINKBIN ]]; then
                READLINKBIN="$(which greadlink)"
                if [[ -z $READLINKBIN ]]; then
                    echo "neither readlink nor greadlink are present. install coreutils with {apt-get,yum,brew} install coreutils" 1>&2
                    exit 1
                fi
            fi

            # test that timeout or gtimeout (darwin) are present
            TIMEOUTBIN="$(which timeout)"

            if [[ -z $TIMEOUTBIN ]]; then
                TIMEOUTBIN="$(which gtimeout)"
                if [[ -z $TIMEOUTBIN ]]; then
                    echo "neither timeout nor gtimeout are present. install coreutils with {apt-get,yum,brew} install coreutils" 1>&2
                    exit 1
                fi
            fi

            # Check for busybox, which has different arguments
            TIMEOUTOUTPUT="$($TIMEOUTBIN --help 2>&1)"
            if [[ "$TIMEOUTOUTPUT" =~ BusyBox ]]; then
                TIMEOUTBIN="$TIMEOUTBIN -t"
            fi
            ;;
    esac
fi
```


## 代码段3

- 定义了`join_array_by_char`函数，用来将数组拼接成字符串，按照OpenSSL的格式：`ciphersuite1:ciphersuite2:...`

```bash
# 得到当前运行目录
DIRNAMEPATH=$(dirname "$0")

# 把数组用分隔符拼接成字符串
join_array_by_char() {
    # Two or less parameters (join + 0 or 1 value), then no need to set IFS because no join occurs.
    if (( $# >= 3 )); then
        # Three or more parameters (join + 2 values), then we need to set IFS for the join.
        local IFS=$1
    fi
    # Discard the join string (usually ':', could be others).
    shift
    # Store the joined string in the result.
    joined_array="$*"
}
```

## 代码段4

- 定义了三种CipherSuite列表
	- `CIPHERSUITE`
	- `SHORTCIPHERSUITE`
	- `FALLBACKCIPHERSUITE`

```bash
# RSA ciphers are put at the end to force Google servers to accept ECDSA ciphers
# (probably a result of a workaround for the bug in Apple implementation of ECDSA)
CIPHERSUITE="ALL:COMPLEMENTOFALL:+aRSA"
# some servers are intolerant to large client hello, try a shorter list of
# ciphers with them
SHORTCIPHERSUITE=(
    'ECDHE-ECDSA-AES128-GCM-SHA256'
    'ECDHE-RSA-AES128-GCM-SHA256'
    'ECDHE-RSA-AES256-GCM-SHA384'
    'ECDHE-ECDSA-AES256-SHA'
    'ECDHE-ECDSA-AES128-SHA'
    'ECDHE-RSA-AES128-SHA'
    'ECDHE-RSA-AES256-SHA'
    'ECDHE-RSA-DES-CBC3-SHA'
    'ECDHE-ECDSA-RC4-SHA'
    'ECDHE-RSA-RC4-SHA'
    'DHE-RSA-AES128-SHA'
    'DHE-DSS-AES128-SHA'
    'DHE-RSA-CAMELLIA128-SHA'
    'DHE-RSA-AES256-SHA'
    'DHE-DSS-AES256-SHA'
    'DHE-RSA-CAMELLIA256-SHA'
    'EDH-RSA-DES-CBC3-SHA'
    'AES128-SHA'
    'CAMELLIA128-SHA'
    'AES256-SHA'
    'CAMELLIA256-SHA'
    'DES-CBC3-SHA'
    'RC4-SHA'
    'RC4-MD5'
)
join_array_by_char ':' "${SHORTCIPHERSUITE[@]}"
SHORTCIPHERSUITESTRING="$joined_array"

# as some servers are intolerant to large client hello's (or ones that have
# RC4 ciphers below position 64), use the following for cipher testing in case
# of problems
FALLBACKCIPHERSUITE=(
    'ECDHE-RSA-AES128-GCM-SHA256'
    'ECDHE-RSA-AES128-SHA256'
    'ECDHE-RSA-AES128-SHA'
    'ECDHE-RSA-DES-CBC3-SHA'
    'ECDHE-RSA-RC4-SHA'
    'DHE-RSA-AES128-SHA'
    'DHE-DSS-AES128-SHA'
    'DHE-RSA-CAMELLIA128-SHA'
    'DHE-RSA-AES256-SHA'
    'DHE-DSS-AES256-SHA'
    'DHE-RSA-CAMELLIA256-SHA'
    'EDH-RSA-DES-CBC3-SHA'
    'AES128-SHA'
    'CAMELLIA128-SHA'
    'AES256-SHA'
    'CAMELLIA256-SHA'
    'DES-CBC3-SHA'
    'RC4-SHA'
    'RC4-MD5'
    'SEED-SHA'
    'IDEA-CBC-SHA'
    'IDEA-CBC-MD5'
    'RC2-CBC-MD5'
    'DES-CBC3-MD5'
    'EXP1024-DHE-DSS-DES-CBC-SHA'
    'EDH-RSA-DES-CBC-SHA'
    'EXP1024-DES-CBC-SHA'
    'DES-CBC-MD5'
    'EXP1024-RC4-SHA'
    'EXP-EDH-RSA-DES-CBC-SHA'
    'EXP-DES-CBC-SHA'
    'EXP-RC2-CBC-MD5'
    'EXP-RC4-MD5'
)
join_array_by_char ':' "${FALLBACKCIPHERSUITE[@]}"
FALLBACKCIPHERSUITESTRING="$joined_array"
```

## 代码段5

- 定义需要使用的变量

```bash
DEBUG=0
VERBOSE=0
DELAY=0
ALLCIPHERS=""
OUTPUTFORMAT="terminal"
TIMEOUT=30
USECOLORS="auto"
# place where to put the found intermediate CA certificates and where
# trust anchors are stored
SAVECRT=""
TEST_CURVES="True"
has_curves="False"
TEST_TOLERANCE="True"
SNI="True"
# openssl formated list of curves that will cause server to select ECC suite
ecc_ciphers=""
TEST_KEX_SIGALG="False"
unset known_certs
declare -A known_certs
unset cert_checksums
declare -A cert_checksums
# array with results of tolerance scans (TLS version, extensions, etc.)
declare -A tls_tolerance
# array with info on type of fallback on unknown sigalgs (or required ones)
declare -A sigalgs_fallback
# array with preferred sigalgs for aRSA and aECDSA ciphers
declare -a sigalgs_preferred_rsa
declare -a sigalgs_preferred_ecdsa
renegotiation=""
compression=""
```


## 代码段6

- `ratelimit`函数：用来控制访问频率
- `usage`函数：打印输出用法，`echo -e`屏蔽换行符
- `verbose`函数：打印日志信息
- `debug`函数：打印调试信息

```bash
# because running external commands like sleep incurs a fork penalty, we
# first check if it is necessary
ratelimit() {
    if [[ $DELAY != "0" ]]; then
        sleep $DELAY
    fi
}

usage() {
    echo -e "usage: $0 [-a|--allciphers] [-b|--benchmark] [--capath directory]
[--saveca] [--savecrt directory] [-d|--delay seconds] [-D|--debug] [-j|--json]
[-v|--verbose] [-o|--openssl file] [openssl s_client args] <target:port>
    usage: $0 -h|--help
    ...
"
}
verbose() {
    if [[ $VERBOSE != 0 ]]; then
        echo "$@" >&2
    fi
}

debug(){
    if [[ $DEBUG == 1 ]]; then
        echo Debug: "$@" >&2
	set -evx
    fi
}
```

## 代码段7

- 定义了`CURVES`：OpenSSL支持的椭圆曲线

```bash
# obtain an array of curves supported by openssl
CURVES=(
    'sect163k1' # K-163
    'sect163r1'
    'sect163r2' # B-163
    'sect193r1'
    'sect193r2'
    'sect233k1' # K-233
    'sect233r1' # B-233
    'sect239k1'
    'sect283k1' # K-283
    'sect283r1' # B-283
    'sect409k1' # K-409
    'sect409r1' # B-409
    'sect571k1' # K-571
    'sect571r1' # B-571
    'secp160k1'
    'secp160r1'
    'secp160r2'
    'secp192k1'
    'prime192v1' # P-192 secp192r1
    'secp224k1'
    'secp224r1' # P-224
    'secp256k1'
    'prime256v1' # P-256 secp256r1
    'secp384r1' # P-384
    'secp521r1' # P-521
    'brainpoolP256r1'
    'brainpoolP384r1'
    'brainpoolP512r1'
)

# many curves have alternative names, this array provides a mapping to find the IANA
# name of a curve using its alias
CURVES_MAP=(
    'sect163k1 K-163'
    'sect163r2 B-163'
    'sect233k1 K-233'
    'sect233r1 B-233'
    'sect283k1 K-283'
    'sect283r1 B-283'
    'sect409k1 K-409'
    'sect409r1 B-409'
    'sect571k1 K-571'
    'sect571r1 B-571'
    'prime192v1 P-192 secp192r1'
    'secp224r1 P-224'
    'prime256v1 P-256 secp256r1'
    'secp384r1 P-384'
    'secp521r1 P-521'
)
```


## 代码段8

- Line: 1897
- 判断输入参数

```bash
# If no options are given, give usage information and exit (with error code)
if (( $# == 0 )); then
   usage
   exit 1
fi

# UNKNOWNOPTIONS=""
while :
do
    case $1 in
        -h | --help | -\?)
            usage
            exit 0      # This is not an error, User asked help. Don't do "exit 1"
            ;;
        -o | --openssl)
            OPENSSLBIN=$2     # You might want to check if you really got FILE
            shift 2
            ;;
        -a | --allciphers)
            ALLCIPHERS=1
            shift
            ;;
        -v | --verbose)
            # Each instance of -v adds 1 to verbosity
            VERBOSE=$((VERBOSE+1))
            shift
            ;;
        -j | -json | --json | --JSON)
            OUTPUTFORMAT="json"
            shift
            ;;
        -b | --benchmark)
            DOBENCHMARK=1
            shift
            ;;
        -D | --debug)
            DEBUG=1
            shift
            ;;
        -d | --delay)
            DELAY=$2
            shift 2
            ;;
        --cafile)
            CACERTS="$2"
            shift 2
            # We need to bypass autodetection if this is provided.
            CACERTS_ARG_SET=1
            ;;
        --capath)
            CAPATH="$2"
            shift 2
            ;;
        --saveca)
            SAVECA="True"
            shift 1
            ;;
        --savecrt)
            SAVECRT="$2"
            shift 2
            ;;
        --curves)
            TEST_CURVES="True"
            shift 1
            ;;
        --no-curves)
            TEST_CURVES="False"
            shift 1
            ;;
        --sigalg)
            TEST_KEX_SIGALG="True"
            shift 1
            ;;
        --tolerance)
            TEST_TOLERANCE="True"
            shift 1
            ;;
        --no-tolerance)
            TEST_TOLERANCE="False"
            shift 1
            ;;
        --colors)
            USECOLORS="True"
            shift 1
            ;;
        --no-colors)
            USECOLORS="False"
            shift 1
            ;;
        --no-sni)
            SNI="False"
            shift 1
            ;;
        --) # End of all options
            shift
            break
            ;;
        # -*)
        #     UNKNOWNOPTIONS=$((UNKNOWNOPTIONS+$1))
        #     # echo "WARN: Unknown option (ignored): $1" >&2
        #     shift
        #     ;;
        *)  # no more options we understand.
            break
            ;;
    esac
done
```

## 代码段9

- Line: 2005
- 赋值`OPENSSLBIN`为`openssl`可执行文件
- 设置当前目录下的`openssl.conf`
- 并且检查`openssl`的`s_client`是否支持`-connect`参数
- 检查参数的兼容性

```bash
if [[ -z $OPENSSLBIN ]]; then
    readlink_result=$("$READLINKBIN" -f "$0")
    if [[ -z $readlink_result ]]; then
        echo "$READLINKBIN -f $0 failed, aborting." 1>&2
        exit 1
    fi
    REALPATH=$(dirname "$readlink_result")
    if [[ -z $REALPATH ]]; then
        echo "dirname $REALPATH failed, aborting." 1>&2
        exit 1
    fi
    OPENSSLBIN="${REALPATH}/${opensslbin_name}"
    if ! [[ -x "${OPENSSLBIN}" ]]; then
        OPENSSLBIN="$(which openssl)"  # fallback to generic openssl
    fi
fi
# use custom config file to enable GOST ciphers
if [[ -e $DIRNAMEPATH/openssl.cnf ]]; then
    export OPENSSL_CONF="$DIRNAMEPATH/openssl.cnf"
fi

OPENSSLBINHELP="$($OPENSSLBIN s_client -help 2>&1)"
if [[ $OPENSSLBINHELP =~ :error: ]]; then
    verbose "$OPENSSLBIN can't handle GOST config, disabling"
    unset OPENSSL_CONF
    OPENSSLBINHELP="$($OPENSSLBIN s_client -help 2>&1)"
fi

if ! [[ $OPENSSLBINHELP =~ -connect ]]; then
    echo "$OPENSSLBIN s_client doesn't accept the -connect parameter, which is extremely strange; refusing to proceed." 1>&2
    exit 1
fi

if [[ -n $CAPATH && -n $CACERTS ]]; then
    echo "Both directory and file with CA certificates specified" 1>&2
    exit 1
fi

if [[ -n $ALLCIPHERS && $OUTPUTFORMAT == "json" ]]; then
    echo "--allciphers cannot produce JSON output, aborting." 1>&2
    exit 1
fi
```

## 代码段10
- Line : 2048
- 检查最后一个参数是否为：`host[:port]`

```bash
# echo parameters left: $@

if (( $# < 1 )); then
    echo "The final argument must be a valid HOST[:PORT], but none was provided." 1>&2
    exit 1
fi

PARAMS=("$@")
last_element="$(( $# - 1 ))"
TARGET=${PARAMS[$last_element]}
unset PARAMS[$last_element]

# Refuse to proceed if the hostname starts with a hyphen, since hostnames can't
# begin with a hyphen and this likely means we accidentally parsed an option as
# a hostname.
if [[ -z $TARGET || $TARGET =~ ^[-:] ]]; then
    echo "The final argument '$TARGET' is not a valid HOST[:PORT]." 1>&2
    exit 1
fi
# Handle Targets that are URIs
if [[ $TARGET =~ /([^/]+)(/|$) ]]; then
    TARGET="${BASH_REMATCH[1]}"
fi
if [[ $TARGET =~ :.*[^0-9] ]]; then
    echo "Final argument is not a valid HOST[:PORT]" >&2
    exit 1
fi
if ! [[ $TARGET =~ : ]]; then
    sni_target=$TARGET
    TARGET="${TARGET}:443"
else
    # strip the port for the sni_target
    if [[ "$TARGET" =~ (.*):([0-9]{1,5}) ]]; then
        sni_target="${BASH_REMATCH[1]}"
    fi
fi

debug "target: $TARGET"
```


## 代码段11
- Line : 2087

```bash
# test our openssl is usable
if [[ ! -x $OPENSSLBIN ]]; then
    OPENSSLBIN=$(which openssl)
    if [[ "$OUTPUTFORMAT" == "terminal" ]]; then
        echo "custom openssl not executable, falling back to system one from $OPENSSLBIN" 1>&2
    fi
fi

if [[ $TEST_CURVES == "True" ]]; then
    if [[ ! -z "$($OPENSSLBIN s_client -curves 2>&1|head -1|grep 'unknown option')" ]]; then
        echo "curves testing not available with your version of openssl, disabling it" 1>&2
        TEST_CURVES="False"
    fi
fi

if [[ -z $CACERTS ]] && ! [[ -n $CACERTS_ARG_SET ]]; then
    # find a list of trusted CAs on the local system, or use the provided list
    for f in /etc/pki/tls/certs/ca-bundle.crt /etc/ssl/certs/ca-certificates.crt; do
        if [[ -e "$f" ]]; then
            CACERTS="$f"
            break
        fi
    done
    if [[ ! -e "$CACERTS" ]]; then
        CACERTS="$DIRNAMEPATH/ca-bundle.crt"
    fi
fi
if ! [[ -e $CACERTS && -r $CACERTS ]]; then
    echo "--cafile $CACERTS is not a readable file, aborting." 1>&2
    exit 1
fi

if [[ -n $CAPATH ]] && ! [[ -d $CAPATH ]]; then
    echo "--capath $CAPATH is not a directory, aborting." 1>&2
    exit 1
fi

if [[ $VERBOSE != 0 ]] ; then
    [[ -n "$CACERTS" ]] && echo "Using trust anchors from $CACERTS"
    echo "Loading $($OPENSSLBIN ciphers -v $CIPHERSUITE 2>/dev/null|grep Kx|wc -l) ciphersuites from $(echo -n $($OPENSSLBIN version 2>/dev/null))"
         $OPENSSLBIN ciphers ALL 2>/dev/null
fi

SCLIENTARGS="${PARAMS[*]}"
# we need the SNI for cscan, so save it if it was provided through
# OpenSSL options
if [[ $SCLIENTARGS =~ servername[\ ]*([^\ ]*)[\ ]* ]]; then
    sni_target="${BASH_REMATCH[1]}"
fi
# only append the SNI:
# if the target is a hostname by validating the tld
# if -servername was not supplied by the user
if [[ $SNI == "True"  && ! $SCLIENTARGS =~ servername ]]; then
    if [[ $sni_target =~ \.[a-zA-Z]{1,20}$ ]]; then
        SCLIENTARGS="$SCLIENTARGS -servername $sni_target"
    else
        echo "Warning: target is not a FQDN. SNI was disabled. Use a FQDN or '-servername <fqdn>'" 1>&2
        sni_target=''
    fi
fi
debug "sclientargs: $SCLIENTARGS"
```