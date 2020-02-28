# Chapter 0
## 0.2 #include
- 기본 영역 (core language) ex. `int` 타입
- 표준 라이브러리 (standard library) ex. `std::ostream` 타입
> Q. 기본 영역은 어떻게 정의되는 집합인가? C의 부분집합인가?

## 0.5 표준 라이브러리를 사용한 출력
- `<<`: STL의 출력 연산자 (output operator)
- 네임스페이스: 관련 있는 기능을 하나의 이름 아래 모아놓은 것 (ex. std, STL이 정의한 모든 기능이 속함)
- `std::cout`: 표준 출력 스트림 (standard output stream)

## 0.7 더 깊이 살펴보기
- 표현식 (expression)
- 부수적 결과 (side effects)
	- ex. `3+4` => no side effect
	- ex. `cout << "side effect" << endl` => 표준 출력 스트림에 Hello, world를 넣는 것이 side effect
- 표현식이 세미콜론(;)으로 끝나면 실행문(expression statement)

cf. std::cout은 std::ostream 타입

### << 연산자
피연산자 2개를 갖는데, 왼쪽 우선 결합성(left-associative)이 있음

1. (std::ostream) << (알 수 없는 타입): 왼쪽 피연산자 스트림에 문자를 넣고 왼쪽 피연산자를 반환함
	- ex. `std::cout << "hello, world!"`
2. (std::ostream) << (manipulator): 오른쪽 조작어의 동작을 실행하고 왼쪽 피연산자를 반환함
	- ex. `(std::cout << std::endl)`

### :: 연산자
범위 연산자. 범위의 이름을 나타내는 왼쪽 피연산자와 범위에서 정의된 이름인 오른쪽 피연산자가 만나 정규화된 이름(qualified name)을 만듦. (ex. `std::cout`)

## 0.8 핵심 정리
- C++ 프로그램은 일반적으로 자유 형식, 즉 공백이 자유롭고 줄바꿈은 공백의 한 종류고 그 이상 의미가 없음. 단 가독성을 위해 들여쓰기를 권장.
- 단 string literals, `#inlcude`, 주석 `//` 세 가지는 자유 요소가 아님. 한 행에 써야 함.
- 예외적으로 main 함수는 return 생략할 수 있으며, return 0이라고 가정함.

## 나의 질문들
Q. 알 수 없는 타입이란 무엇인가? 10.2/284p 참고하라는데 별 내용이 없다.
Q.  `\b` 백스페이스 문자는 어디 쓰이는가? 출력되면 어떻게 보이나?
```cpp
cout<<"Hi";
cout<<'\b';  //Cursor moves 1 position backwards
cout<<" ";   //Overwrites letter 'i' with space
```
https://stackoverflow.com/questions/3745861/how-to-remove-last-character-put-to-stdcout
> You do have to be careful that cout doesn't decide to flush itself before the backspace has been inserted.
> backspace 문자는 커서만 옮긴다.
Q. 실행문에 표현식을 사용하는 것은 선택 사항이기 때문에 세미콜론만 남겨서 null statement를 만들 수 있다. 이건 왜 쓰나?
```cpp
int a[50] = "lord of the rings";
int i;
for(i = 0; a[i] != 't'; i++)
    ;//null statement
```

## 연습문제
### 0-3
> 구현체란 무엇인가 (첫 번째 답변 좋음)
> https://www.quora.com/What-is-an-implementation-of-C++

### 0-9
길이가 가장 짧고 유효한 프로그램은 무엇일까요?
```cpp
int main() {}
```

### 0-10
```cpp
#include <iostream>
// include는 행을 바꾸면 안된다는 예제인 듯함
int
main
()
{
	std::cout
	<<
	"hello world"
	<<
	std::endl;
  return 0;
}
```
