[1. socket](#socket) <br>
[2. setsockopt](#setsockopt) <br>
[3. fcntl](#fcntl) <br>
[4. sockaddr, sockaddr_in](#sockaddr-sockaddr_in) <br>
[5. bind](#bind) <br>
[6. listen](#listen) <br>
[99. 논블록](#논블록) <br>

## socket
``` c
int socket(int domain, int type, int protocol);
```

### 헤더파일
<sys/socket.h>

### 정의
통신을 위한 엔드포인트를 생성

### 반환
소켓 디스크립터 (파일 디스크립터) 반환
에러일 경우 -1 반환

### 파라미터
#### int domain
- AF_INET : IPv4 주소 체계를 사용하는 인터넷 도메인
- AF_INET6 : IPv6 주소 체계를 사용하는 인터넷 도메인

#### int type
- SOCK_STREAM : 연결 지향적인 TCP 소켓으로 신뢰성 있고 순서대로 데이터 전송
- SOCK_DGRAM : 비연결성인 UDP 소켓으로 신뢰성과 순서가 보장되지 않는 데이터 전송

#### protocol
- 소켓의 프로토콜을 지정하는 매개변수
- 주로 0을 사용하여 기본 프로토콜을 선택
- 도메인과 소켓 유형에 따라 자동으로 적절한 프로토콜이 선택되지만, 특정 프로토콜을 명시적으로 지정할 수 있음

## setsockopt
``` c
int setsockopt(int socket, int level, int option_name, const void *option_value, socklen_t option_len);
```

### 헤더파일
<sys/socket.h>

### 정의
소켓의 옵션을 설정

### 반환
설정에 성공했을 경우 0, 실패할 경우 -1

### 파라미터
#### int socket
- 옵션을 설정할 소켓의 파일 디스크립터

#### int level
- 옵션의 레벨/범주

<table><thead><tr><th>프로토콜 레벨</th><th>옵션명</th><th>getsockopt</th><th>setsockopt</th></tr></thead><tbody><tr><td>SOL_SOCKET</td><td>SO_SNDBUF<br>SO_RCVBUF<br>SO_REUSEADDR<br>SO_KEEPALIVE<br>SO_BROADCAST<br>SO_DONTROUTE<br>SO_OOBINLINE<br>SO_ERROR<br>SO_TYPE</td><td>O<br>O<br>O<br>O<br>O<br>O<br>O<br>O<br>O</td><td>O<br>O<br>O<br>O<br>O<br>O<br>O<br>X<br>X</td></tr><tr><td>IPPROTO_IP</td><td>IP_TOS<br>IP_TTL<br>IP_MULTICAST_TTL<br>IP_MULTICAST_LOOP<br>IP_MULTICAST_IF</td><td>O<br>O<br>O<br>O<br>O</td><td>O<br>O<br>O<br>O<br>O</td></tr><tr><td>IPPROTO_TCP</td><td>TCP_KEEPALIVE<br>TCP_NODELAY<br>TCP_MAXSEG</td><td>O<br>O<br>O</td><td>O<br>O<br>O</td></tr></tbody></table>

#### int option_name
- SO_REUSEADDR: 주소 재사용 옵션으로, 이전에 사용된 주소를 재사용할 수 있게 합니다.
- SO_KEEPALIVE: 연결이 활성 상태인지 확인하기 위해 주기적인 keep-alive 패킷을 전송합니다.
- SO_RCVBUF 및 SO_SNDBUF: 수신 버퍼와 송신 버퍼의 크기를 설정합니다.
- SO_LINGER: 연결이 종료될 때 커널에서 소켓을 얼마나 오랫동안 유지할지 지정합니다.

#### const void *option_value
- 설정하려는 옵션 값이 담긴 변수
- 1이면 활성화, 0이면 비활성화

#### socklen_t option_len
- option_value 변수의 크기
- sizeof(option_value)
- unsigned int형

## fcntl
``` c
int fcntl(int fildes, int cmd, ...);
```

### 헤더파일
<fcntl.h>

### 정의
파일 디스크립터에 대한 조작 및 제어를 수행하는 시스템 호출 함수

### 반환값
성공 시 반환값은 cmd에 따라 다름
- F_DUPFD: 새로운 파일 디스크립터가 생성되고 해당 파일 디스크립터가 반환됩니다.
- F_GETFD: flag의 값이 반환됩니다. (비트 연산을 통해 특정 플래그 값을 확인할 수 있습니다.)
- F_GETFL: flags의 값이 반환됩니다. (파일 디스크립터의 상태 플래그 값을 확인할 수 있습니다.)
- F_GETOWN: 파일 디스크립터 소유자의 값이 반환됩니다.
- 그 외 : -1이 아닌 다른 값

실패 시 반환값은 -1

### 파라미터
#### int fildes
- 조작하고자 하는 파일 디스크립터

#### int cmd
- F_DUPFD: 파일 디스크립터의 복제를 수행합니다.
- F_GETFL: 파일 상태 플래그를 조회합니다.
- F_SETFL: 파일 상태 플래그를 설정합니다.
- F_SETLK: 파일 잠금(락)을 설정합니다.
- F_SETOWN: 파일 소유자(Process ID)를 설정합니다.

#### ...
설정하고자 하는 파일 상태 플래그 값을 나타내는 정수 값

cmd가 F_SETFL일 경우,
- O_RDONLY: 읽기 전용으로 파일을 열기 위한 플래그입니다.
- O_WRONLY: 쓰기 전용으로 파일을 열기 위한 플래그입니다.
- O_RDWR: 읽기 및 쓰기 모드로 파일을 열기 위한 플래그입니다.
- O_APPEND: 파일의 끝에 추가하여 쓰기를 수행하는 플래그입니다.
- O_NONBLOCK: 논블로킹 모드로 파일을 열기 위한 플래그입니다.
- O_CREAT: 파일이 존재하지 않으면 새로운 파일을 생성하는 플래그입니다.

## sockaddr, sockaddr_in
``` c
struct sockaddr_in {
	__uint8_t       sin_len;
	sa_family_t     sin_family;
	in_port_t       sin_port;
	struct  in_addr sin_addr;
	char            sin_zero[8];
};

struct sockaddr {
	__uint8_t       sa_len;         /* total length */
	sa_family_t     sa_family;      /* [XSI] address family */
#if __has_ptrcheck
	char            sa_data[__counted_by(sa_len - 2)];
#else
	char            sa_data[14];    /* [XSI] addr value (actually smaller or larger) */
#endif
};
```

### 헤더파일
<arpa/inet.h>

### 정의
네트워크 프로그래밍에서 IPv4 소켓 주소를 나타내기 위해 사용되는 구조체로 주로 소켓을 바인딩하기 위해 사용

### 멤버 변수
#### __uint8_t sin_len
- 구조체의 길이를 나타내는 필드

#### sa_family_t sin_family
- 주소패밀리를 나타내는 필드
> 주소패밀리란 네트워크 프로토콜에서 사용되는 주소 체계를 식별하는데 사용되는 값 <br>
> 특정 네트워크 프로토콜 패킷을 어떻게 라우팅하고, 주소를 해석하고, 데이터를 전송하는지 정의
- IPv4를 나타내는 소켓인 경우 AF_INET으로 설정

#### in_port_t sin_port
- 포트 번호를 나타내는 필드
- htons 함수를 사용하여 호스트 바이트 순서에서 네트워크 바이트 순서로 변환하여 설정
> uint16_t	htons(uint16_t hostshort) <br>
> - unsigned short값을 Big-endian(네트워크 바이트 순)으로 바꿈
> - 0x1234의 경우 호스트 바이트 순서에선 0x12 0x34로 표현되지만 htons를 이용하면 네트워크 바이트 순서인 0x3412로 변환

#### struct in_addr sin_addr
- IPv4 주소를 나타내는 필드
- inet_addr과 htonl을 주로 사용
> in_addr_t	inet_addr(const char *cp) <br>
>	- 문자열 형태의 IPv4 주소를 32비트 정수형으로 변환

> uint32_t	htonl(uint32_t hostlong) <br>
>	- unsigned long 값을 Big-endian(네트워크 바이트 순)으로 바꿈
>	- 0x12345678의 경우 호스트 바이트 순서에선 0x12 0x34 0x56 0x78로 표현되지만 htonl을 이용하면 네트워크 바이트 순서인 0x78 0x56 0x34 0x12로 변환

#### char sin_zero[8]
- 패딩을 위한 8비트의 더미 필드
- 구조체의 크기를 맞추기 위해 사용되며, 보통 0으로 설정

### sockaddr vs sockaddr_in
- sockaddr 구조체는 주소 패밀리와 주소 데이터를 포함하는 구조체로, 다양한 주소 패밀리를 지원함
- sockaddr_in 구조체는 sockaddr 구조체를 기반으로 확장된 형태로 더 IP에 대한 더 자세한 정보를 포함하고 있음
- 호환성을 위해 더 많은 주소 패밀리를 포함할 수 있는 sockaddr을 사용하며, 구조체의 크기가 더 큰 sockaddr_in을 이용해 유연성을 확보할 수 있음

## bind
```c
int bind(int sockfd, const struct sockaddr *addr, socklen_t addrlen);
```

### 헤더파일
<sys/socket.h> <br>
<sys/types.h>

### 정의
소켓을 로컬 IP 주소와 포트에 바인딩하는 역할

### 반환
성공 시 0, 실패 시 -1

### 파라미터
#### int sockfd
- 바인딩 할 소켓의 파일 디스크립터

#### const struct sockaddr *addr
- 바인딩 할 로컬 IP 주소와 포트를 포함하는 구조체 포인터

#### socklen_t addrlen
- addr 구조체의 크기
- 일반적으로 sizeof(struct sockaddr_in)을 사용

## listen
```c
int listen(int sockfd, int backlog);
```

### 헤더
<sys/socket.h> <br>
<sys/types.h>

### 정의
- 소켓을 수신 대기 상태로 설정하는 함수
- 클라이언트의 연결 요청을 수신하기 위해 사용

### 반환
성공 시 0, 실패 시 -1

### 파라미터
#### int sockfd
- 수신 대기 상태로 설정할 소켓의 파일 디스크립터

#### int backlog
- 대기 큐의 최대 연결 요청 수
> 대기 큐 <br>
>	- 클라이언트의 연결 요청을 임시로 보관하는 공간
>	- 서버가 연결을 수락할 준비가 되었을 때 대기 큐에서 순차적으로 연결 수락
- 대기 큐의 길이를 제한하여 동시에 처리할 수 있는 연결 요청의 수를 조절하는 역할

## 논블록
1. fcntl에 O_NONBLOCK옵션을 주어 파일 열고 닫기에 논블로킹
2. select, poll, epoll, kqueue 등 I/O 멀티플렉싱으로 I/O 작업 블로킹