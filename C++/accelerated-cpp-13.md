# Chapter 13

## 13.1
- 기본 클래스의 생성자, 할당 연산자, 소멸자를 제외한 모든 멤버는 파생(상속)된 클래스에 상속됨
- `public` 레이블: 기본 클래스의 `public` 인터페이스 상속
- `protected` 레이블: 파생 클래스가 기본 클래스 객체를 이루는 `protected` 멤버에 접근할 수 있게 만듦

```cpp
double Grad::grad() const {
  return min(Core::grad(), thesis);
  // return min (grad(), thesis); // Grad 클래스의 grad()를 재귀적으로 호출
}
```

#### 파생 객체가 생성되는 과정
1. 기본 클래스, 파생 클래스 멤버를 모두 포함하는 크기의 공간 할당
2. 기본 클래스 생성자 호출 => 기본 클래스에서 상속한 부분 초기화
3. 생성자 이니셜라이저로 파생 클래스 멤버 초기화
4. 파생 클래스 생성자 본문 실행

기본 클래스 생성자를 지정하지 않은 경우 기본 클래스의 기본 생성자가 사용됨

## 13.2

#### 가상 함수 (virtual function)
- 런타임 시점에 객체 타입에 따라 함수를 선택
- 참조 또는 포인터로 호출할 때만 사용되는데, 기본 클래스 참조/포인터는 파생된 타입을 가리킬 수 있기 때문
- 객체로 호출할 때는 컴파일 과정에서 객체 타입을 확실히 알게 되어 가상 함수 사용하지 않음
- 가상 함수는 상속됨

## 13.3

```cpp
vector<Core> students;
Core record;
```

위 코드의 경우, 기본 Core 타입만 저장할 수 있으므로 포인터로 바꿈

```cpp
vector<Core*> students;
Core* record;
```

#### 가상 소멸자

`delete`를 호출하면
1. 해당 객체에 소멸자가 실행되며
2. 객체가 점유하는 저장 공간이 해제됨

`core*` 타입을 `delete`할 때, 어떤 소멸자를 실행할지, 얼마만큼의 저장 공간을 반환할지 결정하기 위해 가상 소멸자를 사용한다.

```cpp
class Core {
  public:
    virtual ~Core() {}
};
```

## 13.4

#### 핸들 클래스
객체를 가리키는 포인터를 은닉화하는 새로운 클래스

#### 정적 멤버 함수
```cpp
class Student_info {
public:
  static bool compare(const Student_info& s1, const Student_info& s2) {
    return s1.name() < s2.name();
  }
}
```

- 멤버 함수와 달리 클래스와 연관될 뿐 객체와 연관되지 않는 함수
- 클래스 객체의 정적이지 않은 데이터 멤버에 접근할 수 없음
- 여기서는 전역으로 선언한 `compare()` 함수를 오버로드하지 않고 의도적으로 `Student_info::compare()`를 구분해서 호출하기 위해 사용함

#### public, proctected
`clone()`(13.4.2) 함수는 인터페이스가 아닌 구현의 산물이기 때문에 `public`이 아닌 `protected`에 두어야 한다.

#### friend 클래스
- `friend` 함수: 클래스의 `private`, `protected`에 접근할 수 있음
- `friend` 클래스: 해당 클래스의 모든 멤버를 `friend`로 만드는 것과 같음

```cpp
class A {
  frined class B;
};
```
B 클래스는 A 클래스의 `private`, `proctected` 레이블에 속한 모든 멤버에 접근할 수 있음

#### 포인터를 리턴하는 함수 상속하기
```cpp
class A {
  A* clone() { return new A(*this); }
};

class B {
  B* clone() { return new B(*this); }
}
```
일반적으로 파생 클래스에서 기본 클래스 함수를 재정의할 때는 매개변수 목록과 반환 타입이 기본 클래스와 같아야 하나, 기본 클래스의 포인터 또는 레퍼런스를 반환하는 경우에는 파생 클래스의 포인터 또는 레퍼런스를 반환할 수 있다.

## 13.6
#### 상속과 컨테이너 타입
```
vector<Core> students;
Grad g();
studnets.push_back(g);
```

Core 클래스에서 상속된 부분만 복사된다.

#### 호출할 함수의 선택
기본 클래스와 파생 클래스의 멤버 함수가 같은 이름일 때, 매개변수 개수와 타입이 정확히 일치하지 않으면 관련 없는 함수처럼 동작함

```cpp
Void Core()::foo(int i);
Void Grad()::foo(int i1, int i2);

Core c;
c.foo(0); // Core::foo 호출
c.foo(0, 0); // 컴파일 에러

Grad g;
g.foo(0); // 컴파일 에러. Grad::foo(int, int)함수 때문에 Core::foo(int)는 숨겨짐
g.foo(0, 0) // Grad::foo 호출
g.Core::foo(0) // Core::foo 정상적 호출

// foo를 가상 함수로 구성하기 위한 인터페이스 (기본 인수를 제공함)
virtual void Core::foo(int i, int = 0); 
```

## 연습문제
### 13-9

`if (s.cp)` 검사에서 통과하지 못해 무조건 0으로 할당된다.
