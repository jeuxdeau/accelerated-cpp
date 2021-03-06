# Chapter 1. 문자열 사용

## 1.1 입력
- 객체(object): 타입이 정해진, 컴퓨터 메모리의 일부
  - 인터페이스(interface): 어떤 타입의 객체로 할 수 있는 동작의 모음
- 변수(variable): 이름이 있는 객체 (이름 + 타입)
  - cf. 이름 없는 객체도 존재함 `"string object"`
  - 변수가 소멸할 때 메모리를 반환함
  
```cpp
std::cin >> name;
// >> 연산자는 표준 입력 스트림에 있는 모든 공백을 무시한 후
// 표준 입력으로 문자열을 읽어 name에 저장
// 다음 공백 문자나 EOF가 나타날 때까지 읽음
```

**버퍼**  
버퍼에 출력할 문자를 모으고, 필요할 때만 저장한 내용을 출력하고, 버퍼를 비운다.

1. 버퍼가 가득 차 있으면 자동으로 비움
2. 표준 입력 스트림으로 입력 내용을 요청받으면 버퍼를 비움
3. 명시적인 코드로 버퍼를 비움

> 우빈: 버퍼를 (비우는 것)과 (출력하는 것)은 다른 일이다.

## 1.3 핵심 정리
`wchar_t`: 확장 문자(ex. 한국어)를 저장하는 기본 타입

## 연습문제
### 1-1.
올바름

### 1-2.
올바르지 않음. `"x" + "y"`에서 x, y 모두가 스트링 문자열 리터럴 또는 char 타입일 수는 없음.  
cf. `string` + `string`은 가능함.

### 1-3, 4.
동작함

### 1-5.
`x`가 정의되지 않음

### 1-6.
```
Samuel
Beckett
```
각각 출력됨. 먼저 버퍼에 `Samuel Beckett`이 담기고, cin이 버퍼에서 이를 읽어 공백 이전 `Samuel`까지 name에 넣는다.
이후 다시 cin으로 입력을 요청받으면 버퍼에 남아 있는 `Beckett`를 넣고 버퍼를 비운다.
