# Chapter 12

클래스를 정의할 때는 해당 클래스 객체가 값처럼 동작하는 정도를 정의한다. 즉, 객체 각각이 다른 객체들과 독립적인 상태가 되도록 만들 수 있다.

# 12.1
#### 템플릿 생성자
```cpp
class Str {
public:
  template<class In> Str(In b, In e) {
    std::copy(b, e, std::back_inserter(data));
  }
}
```
클래스는 템플릿 함수를 멤버로 삼을 수 있다. 즉, 바인딩되지 않은 같은 이름의 멤버 함수 집단을 정의할 수 있다. 위와 같는 생성자를 템플릿 함수로 만들어 여러 타입의 반복자를 사용하도록 하였다.

## 12.2
#### 사용자 정의 변환(user-defined conversion)
클래스 타입에서 다른 클래스 타입으로 변환하는 방법
1. 다른 타입에서 해당 타입으로 변환: 인수 하나가 있는 생성자를 정의함
2. 해당 타입에서 다른 타입으로 변환

## 12.3
#### istream& unget();
입력 스트림을 문자 하나만큼 역행해 가장 최근에 호출한 get 함수의 동작을 취소함. 즉, 직전의 get 함수가 동작하지 않은 상태로 만듦.

#### friend 관계
`private` 멤버에 대한 접근 권한을 부여함. `friend` 선언문은 순서상 `private`나 `public` 앞뒤 어디든 위치할 수 있지만, 클래스 시작 부분 `public` 인터페이스와 이웃하여 모아 두면 좋다.

#### 변환 연산 과정
```cpp
Str greeting = "Hello, " + name + "!";

Str temp1("Hello, "); // Str::Str(const char*)
Str temp2 = temp1 + name; // operator+(const Str&, const Str&)
Str temp3("!") // Str::Str(const char*)
Str greeting = temp2 + temp3; // operator+(const Str&, const Str&)
```
이와 같은 자동 변환은 임시 객체를 만들어 컴퓨터 자원을 많이 사용한다. 따라서 상용 문자열 라이브러리들은 자동 변환에 의지하지 않고, 피연산자들의 모든 조합에 대한 결합 연산자를 정의하는 방식을 따른다.

#### 이항 연산자 설계
클래스가 자동 변환이 된다면 이항 연산자를 멤버 함수로 정의하지 말아야 한다. 예를 들어 `x + y`와 같은 표현식을 작성할 때, `x`가 어떤 타입으로 변환될지도 모른다면 컴파일러는 `operator+` 함수를 멤버로 갖는 타입으로 `x`를 변환할 수 있는지 모든 타입에 대해 검사해야 한다. 이항 연산자를 멤버 함수로 정의하지 않는다면 컴파일러는 `x`의 타입 클래스 멤버의 `operator+`와 멤버가 아닌 ㅐ

## 연습문제
### 12-15
`istrem::get` 함수가 입력 스트림의 상태를 확인하고 EOF, fail, bad 등의 상태 플래그를 반환하기 때문에 따로 스트림을 검사해줄 필요가 없다.
> cf. http://www.cplusplus.com/reference/istream/istream/get/
