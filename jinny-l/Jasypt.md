# [Spring] Jasypt로 yml 파일 암호화 및 암호화 키를 환경변수로 저장하는 방법

# 💬 들어가기 전에

팀 프로젝트를 하면서 협업을 하면서 관리해야 하는 yml 파일이 늘어나며
yml 파일 관리의 필요성과 중요성을 느꼈다.

지금까지 코드스쿼드 팀 미션을 4번 진행하면서 yml 파일 관리하는 방법도 점차 성장한 것 같아
그 과정을 기록해 보려고 한다.

참고로 사용하는 방법은 본문에 있으니, 급하면 밑의 [본문](https://velog.io/@jinny-l/spring-jasypt-encrypt-yml-and-store-encryption-key-as-environment-variable#1-bean%EC%9C%BC%EB%A1%9C-jasypt-%EC%95%94%ED%98%B8%ED%99%94-%EC%84%A4%EC%A0%95)부터 보면 된다.

4번의 팀프로젝트 과정을 요약해보면 다음과 같다.

<br/>

## ☕️ 첫번째 팀 프로젝트 - [Kiosk 미션](https://github.com/codesquad-gwanaksan/kiosk-max)

### yml 관리 방법 
- github에 올리지 않고 각자 `local`에서 관리
- 사용하는 기술이 많지 않아 `yml`에는 DB 정보밖에 없어서 딱히 불편함은 느끼지 못했다.

### 단점

- 하지만 관리해야 하는 `yml` 파일이 늘어날 수록, 또 팀원이 늘어날 수록 파일 관리가 어려운 단점이 있다.

<br/>

## ✅ 두번째 팀 프로젝트 - [Todo App 미션](https://github.com/codesuqad-team3-to-do-list/todo-max)

### yml 관리 방법 
- [`Jasypt`을 활용하여 파일 암호화 - `Bean`으로 설정](https://velog.io/@jinny-l/spring-jasypt-encrypt-yml-and-store-encryption-key-as-environment-variable#1-bean%EC%9C%BC%EB%A1%9C-jasypt-%EC%95%94%ED%98%B8%ED%99%94-%EC%84%A4%EC%A0%95)
- 본문에서 다룰 내용인데 `Jasypt`로 파일 암호화를 해서 Github 레포지토리에 올렸다.

### 첫번째 프로젝트 때의 단점 해결
- 이렇게 하면 별도로 파일 관리를 하지 않아도 되고 
팀원이 모두 같은 버전의 파일을 사용하고 있다는 보장을 할 수 있다.

### 문제: 배포 시 복호화 키 주입하는 방법
local에서는 `Jasypt` 복호화 키를 `IntelliJ`에서 환경변수로 설정해서 사용하였지만
배포 시에 `Jasypt`의 암호화 키를 어떻게 넣어줘야 하는지 몰라서 헤맸다. 

[ToDo App 팀 프로젝트 회고 글](https://velog.io/@jinny-l/codesquad-retrospective-ToDo-App-team-project#1-2-%EC%83%88%EB%A1%9C-%EC%8B%9C%EB%8F%84%ED%95%B4-%EB%B3%B8-%EA%B2%83)에 작성했던 내용인데 
jar 실행할 때 복호화 키를 주입하는 방법을 몰라서 찾아보다가 `Java vm option`에 대해 알게 되었다.

이때는 `vm option`이 뭐가 있는지 몰라서 
결국엔 서버에 미리 `application.yml` 작성해놓고 대체하는 방법을 사용했지만 다음 프로젝트 때 개선했다.

> _[ToDo App 팀 프로젝트 회고 글](https://velog.io/@jinny-l/codesquad-retrospective-ToDo-App-team-project) 일부 내용:_
> ![](https://velog.velcdn.com/images/jinny-l/post/aa99ce66-1032-45c4-b8f7-ce6a0ef575e3/image.png)

<br/>

## 📈 세번째 팀 프로젝트 - [Issue Tracker 미션](https://github.com/eojjeogo-jeojjeogo/issue-tracker-max)

### yml 관리 방법 
- [`Jasypt`을 활용하여 파일 암호화 - `yml`로 설정](https://velog.io/@jinny-l/spring-jasypt-encrypt-yml-and-store-encryption-key-as-environment-variable#2-yml%EB%A1%9C-jasypt-%EC%95%94%ED%98%B8%ED%99%94-%EC%84%A4%EC%A0%95)
- 이전 프로젝트와 똑같이 `Jasypt` 기술을 활용하여 내용 암호화를 진행했지만 
이번에는 `Bean`으로 등록하지 않고 `yml`로 설정해서 사용했다.


### 두번째 프로젝트 때의 문제 해결
두번째 프로젝트 때 배포 시 복호화 키 주입하는 방법을 몰라 헤맸는데
`vm option`을 더 공부해보니 배포할 때 `-Djasypt.encryptor.password={시크릿키}`를 `option`으로 주면된다.

<br/>

## 🍆 네번째 팀 프로젝트 - [Second Hand 미션](https://github.com/masters2023-project-team05-second-hand/second-hand-max-be-b)

지금 막 시작한 프로젝트인데 `Github Actions`와 `docker-compose`로 배포를 진행하고 있다.

### 문제: 환경 변수 관리

앞서 경험한 것들을 총 집합해서 구성했지만 `Dockerfile`에 시크릿키를 전달해야 하는 이슈가 있다.

물론 `docker-compose.yml` 파일을 통해 변수를 직접 주입해주면 되지만 
`docker-compose.yml`도 버전 관리를 위해 `Github`에 올리고 있으므로 
`Github`에는 환경변수를 작성하지 않은 파일을 올려야 했고, 서버에서는 직접 변수를 입력해줘야 하는 번거로움이 있다. 

그래서 `docker-compose` 파일에 시크릿키를 직접 작성하지 않을 수 있는 방법을 찾아보았다.

서버(EC2)에 환경변수 파일을 만들어 놓고 변수를 주입받으면 된다.

이 내용도 [본문](https://velog.io/@jinny-l/spring-jasypt-encrypt-yml-and-store-encryption-key-as-environment-variable#3-%EC%84%9C%EB%B2%84-%ED%99%98%EA%B2%BD%EB%B3%80%EC%88%98-%EC%84%A4%EC%A0%95)에서 다룰 예정이다.

---

<br/>

# 1. `Bean`으로 `Jasypt` 암호화 설정

> 공식 홈페이지: http://www.jasypt.org/

## 1-1. build.gradle에 의존성 추가

```java
implementation 'com.github.ulisesbocchio:jasypt-spring-boot-starter:3.0.5'
```


## 1-2. Bean 등록
- `Config` 클래스 작성

```java

@Configuration
class JasyptConfig {
	
//    @Value("${jasypt.encryptor.password}") 외부 환경변수를 어노테이션으로 주입받아도 되고 아래처럼 설정하는 방법도 있다.
//    private String JASTYPTKEY;

    @Bean("jasyptStringEncryptor")
    public StringEncryptor stringEncryptor() {

        PooledPBEStringEncryptor encryptor = new PooledPBEStringEncryptor();
        SimpleStringPBEConfig config = new SimpleStringPBEConfig();
		
        // !!주의: 암복호화에 사용되는 키로 1-4번 과정에서 설정하는 변수명이랑 동일해야 한다. 
        config.setPassword(System.getenv("JASYPTKEY")); 
        
        config.setAlgorithm("PBEWITHMD5ANDDES"); // 암호화 알고리즘
        config.setPoolSize("1");				 // 암호화 인스턴스 pool
        config.setKeyObtentionIteration("1000"); // 반복할 해싱 횟수
        config.setSaltGeneratorClassName("org.jasypt.salt.RandomSaltGenerator"); // salt 생성 클래스
        config.setStringOutputType("base64");    // 인코딩 방식
        
        encryptor.setConfig(config);
        return encryptor;
    }
}
```

> 💡 참고:
> - `saltGenerator`를 지정하지 않으면 `default` 값으로 `RandomSaltGenerator`를 사용한다.
> - 고정된 `salt` 값을 사용하려면 `StringFixedSaltGenerator`를 사용하면 된다.
>   - 이 경우 암호값이 동일하고 복호화 시에도 기존에 설정한 `salt`와 동일한 값을 지정해야 복호화가 가능하다.




이 외에도 `config` 설정이 더 있는데 보다 상세한 내용은 아래 URL에서 확인 가능하다.
- https://github.com/ulisesbocchio/jasypt-spring-boot#use-you-own-custom-encryptor

## 1-3. 내용 암호화
- [암호화 사이트](https://www.devglan.com/online-tools/jasypt-online-encryption-decryption)에서 암호화를 진행하고 나온 결과를 `yml` 파일에 `ENC()`로 감싸서 작성한다.

**application.yml 예시**
```yml
spring:
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: ENC(URcBC2us4EiLrKbeiOdQIGnOz8USQI3Ct6VO9M0geIwetzGFzoMKJPupbvYgrv95uy8+2vdcRKq/xNfo9n367v9y/dFFHwSghtNYo5BX27iJpvCMoY4F+tXmV6RBjy6AlXyCifu+QMorBD+EpA7c2jDGZh3OQx6Y)
    username: ENC(aaAZtXujmwi47WvLzuFZEA==)
    password: ENC(msP9S4m29/R0bw6IN3Q6Kuasi4h4UENu)
```



## 1-4. Jar 실행 시 암복호화키 전달

### IntelliJ
- 환경변수 추가

1. 인텔리제이 상단에 `Edit Configurations...` 클릭
![](https://velog.velcdn.com/images/jinny-l/post/319b9b2c-72b7-493f-8017-515327cec215/image.png)

2. Environment variables 영역에 변수 작성
![](https://velog.velcdn.com/images/jinny-l/post/b3129217-814f-4c0c-b495-b0fe874e934e/image.png)

### 스크립트
```shell
 java -jar -DJASYPTKEY=secret-key app.jar
```

---
<br/>

# 2. `yml`로 `Jasypt` 암호화 설정

## 2-1. 의존성 추가

```java
implementation 'com.github.ulisesbocchio:jasypt-spring-boot-starter:3.0.5'
```

## 2-2. yml 설정 파일 작성

```yml
jasypt:
  encryptor:
    iv-generator-classname: org.jasypt.iv.NoIvGenerator
    algorithm: PBEWithMD5AndTripleDES
    password: # 2-3번 과정때 사용하고 이후 지워야 함
```

> ⚠️ 주의:
> - `password`에 암복화에 사용될 키를 작성하는데 해당 내용은 2-3 과정에 필요하다. 
> - 공개된 곳에 올릴 때는 지워야 한다.

## 2-3. 내용 암호화

`Bean`으로 등록했을 때처럼 암호화 사이트에서 암호화해도 되는데 해당 방법은 위에서 사용했으니
이번에는 다른 방법으로 암호화해보겠다.

출력에 필요한 클래스 파일을 임시로 하나 생성해서 암호화하는 방법이다.

- 임시로 클래스 하나 생성

```java
@SpringBootTest
class SecondHandApplicationTests {

    @Autowired
    StringEncryptor stringEncryptor;

    @Test
    void encrypt() {
        String encrypt = stringEncryptor.encrypt("secret-key");
        System.out.println("encrypt = " + encrypt);
    }
}
```

- 출력된 내용을 yml 파일에 작성
![](https://velog.velcdn.com/images/jinny-l/post/40ffb5dc-71e2-4930-a590-75e03dee2c56/image.png)


## 2-4. Jar 실행 시 암복호화키 전달

### IntelliJ
- 2-2번 과정에서 삭제한 password를 전달해야 한다.

1. 인텔리제이 상단에 `Edit Configurations...` 클릭

2. `Modify options` > `Add VM options` 클릭
![](https://velog.velcdn.com/images/jinny-l/post/17fce9df-5508-4af5-acea-a6e40ba3c5fb/image.png)
![](https://velog.velcdn.com/images/jinny-l/post/59a828c5-0161-422a-bf5b-2aa248edb64d/image.png)

3. `vm options`에 `password` 작성

```shell
-Djasypt.encryptor.password=key
```

![](https://velog.velcdn.com/images/jinny-l/post/812271ab-f9b0-43b5-a254-5b2bcaa457b1/image.png)

### 스크립트
```shell
java -jar -Djasypt.encryptor.password=$JASYPT_PASSWORD app.jar
```

---
<br/>

# 3. 서버 환경변수 설정

## 3-1. 환경변수 설정

서버에 환경변수를 미리 작성해두고 배포 시 활용할 수 있다.

**1. `Vim`으로 `bash_profile`을 생성한다.**
```shell
vi ~/.bash_profile
```

**2. 해당 파일에 환경변수 작성**
```shell
export MY_NAME=Jinny
export JASYPT_PASSWORD=secret
```

**3. 설정 적용**
```shell
source ~/.bash_profile
```

**4. 변수 적용 확인**
```shell
echo $MY_NAME
```

> - 잘 적용되었다면 아래와 같이 변수가 잘 출력되는 모습을 볼 수 있다.
> ![](https://velog.velcdn.com/images/jinny-l/post/0b7d9f71-3301-4f7e-a4e9-54e6f72693f1/image.png)


## 3-2. Docker 배포 시 환경변수 활용

**1. `Dockerfile` 작성** 
- 도커파일에 아래와 같이 변수를 작성하고 docker-compose 파일을 통해 주입받는다.
```shell
# 생략...
ENTRYPOINT ["java", "-jar", "-Djasypt.encryptor.password=${JASYPT_PASSWORD}", "/app.jar"]

```

**2. `docker-compose.yml` 작성** 
- `docker-compose` 파일 내의 변수는 `.bash_profile`의 변수로부터 전달 받는다.
- 즉 다음과 같이 전달되는 것이다.
	- `.bash_profile` 내의 변수 → `docker-compose` → `Dockerfile`

```shell
services:
  was:
    container_name: 
    image: 
    environment:
      - JASYPT_PASSWORD=${JASYPT_PASSWORD}
```

> ⚠️ 참고: 
> 그런데 어쩔 때는 환경 변수가 잘 적용되고 어쩔 때는 적용이 안될 때가 있다.
> 구글링을 해보니 이런 글을 찾았다. 뭔가 도커 버그가 아닌가 싶다.
>> _만약 도커를 사용하여 컨테이너 실행 시 환경변수를 사용하고싶다면 버전은 3.0.4를 필수로 사용해야한다. 3.0.3으로 한참을 시도했는데 안되다가 3.0.4버전 사용 시 잘 실행되었다._
>> 출처: https://velog.io/@rnjsals1575/Jasypt%EB%A5%BC-%EC%82%AC%EC%9A%A9%ED%95%9C-%EB%AF%BC%EA%B0%90%EC%A0%95%EB%B3%B4-%EC%95%94%ED%98%B8%ED%99%94

---

# 🧞‍♂️ 소결

`Jasypt`를 사용하면서 느낀점은...
지금처럼 팀미션을 진행할 때 사용하는 것은 나쁘지 않은 것 같다.

하지만 암호화는 항상 뚤릴 위험이 있기 때문에 실제 실무에 들어가면 잘 사용하지 않을 것 같다. 
내가 사장이면 안쓴다.!

그렇다면 또 무슨 방법이 있냐!!하면 `Github`의 `submodule`을 활용하는 것인데
아직 사용해보지 못해서 기회가 되면 블로그글을 작성해보겠다.

---

# 참고:
- 팀원 Ape, Albert의 지식 공유
- Jasypt 설정 관련
  - https://luvstudy.tistory.com/67
  - https://kitty-geno.tistory.com/160
  - https://yjkim97.tistory.com/64
- 배포 서버 환경 변수 설정 관련
  - https://bibi6666667.tistory.com/319 
