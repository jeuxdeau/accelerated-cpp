# Chapter 11

## 11.2
#### 템플릿 클래스
타입이 다른 여러 버전의 클래스를 만들 수 있음
```cpp
template <class T> class Vec {
  private:
    T* data;
}

Vec<int> v;
```

#### explicit
프로그래머가 명시적으로 생성자를 호출하는 상황에서만 해당 생성자를 사용하도록 함

```cpp
class A {
public:
  explicit A(int b) {}
}

A a(100); // 정상, 명시적으로 생성
A a = 100; // 암시적으로 A(100)을 생성하고 a에 복사하려 하나, explicit 때문에 불가능
```

#### 반복자의 타입
일반적으로 반복자는 클래스 타입임

#### 연산자 오버로딩
연산자를 오버로딩할 때는 `operator` 키워드와 연산자 기호를 붙여 구성한다. 예를 들어 `[]` 기호를 오버로딩할 때는 `operator[]` 이름의 함수를 정의한다.

해당 함수의 매개변수는 연산자 종류에 따라 다른데,
1. 연산자가 멤버 함수일 경우, 연산자를 호출한 객체가 왼쪽 피연산자에 암시적으로 바인딩되어 실제로 필요한 것보다 하나 적은 인수를 갖게 됨
2. 연산자가 멤버가 아닌 함수인 경우, 연산자의 피연산자 개수만큼 인수를 갖게 됨

## 11.3
#### 명시적 복사
```cpp
vector<int> a;
vector<int> b = a; // a를 b로 복사
```

#### 암묵적 복사
```cpp
vector<int> v;
double d;
d = median(v); // v를 median 함수의 매개변수로 복사

string line;
vector<string> words = split(line); // split 함수에서 반환하는 객체를 words로 복사
```

#### 복사 생성자
명시적, 암묵적 복사를 제어하는 생성자. 인수로는 클래스 타입 인수 1개를 가지는데, 사본을 만들 객체를 의미함. 이때 이 인수는 기존 객체를 바꿔서는 안 되므로 `const&` 타입으로 전달한다.

```cpp
class A {
public:
  A (const A& a) // 복사 생성자
}
```

복사란 단순히 데이터를 복사하는 것이 아닐 수 있다. 예를 들어, 표준 벡터를 복사해도 데이터가 저장되는 공간은 공유되지 않는다.

#### 할당 연산자
기존에 존재하는 값을 제거하고 새로운 값으로 대체하는 동작. 복사는 처음으로 새로운 객체를 만드는 것이지만, 할당은 기존 객체를 해제한다. 할당 연산자는 클래스의 멤버로, 좌변(호출한 객체)에 대한 참조 타입을 반환해야 한다.

```cpp
template <class T> class Vec {
public:
  // 헤더 파일에서는 템플릿 매개변수를 생략 가능함
  //Vec<T>& operator= 라고 쓰지 않아도 됨
  Vec& operator=(const Vec&);
}

// 이미 vec<T>의 operator임을 명시한 이상
// 매개변수를 Vec<T>&로 나타내지 않아도 됨
template <class T> vec<T>& Vec<T>::operator=(const Vec& rhs) {
// 자가 할당 여부 확인에 주의
  if (&rhs != this) {
    uncreate();
    create(rhs.begin(), rhs.end());
  }
  return *this;
}
```

#### this
멤버 함수에서 동작하는 객체를 가리키는 포인터. 객체 자신을 참조해야 할 때 사용한다.

#### 할당과 초기화의 차이점
```cpp
string s1 = "abc"; // 초기화
string s2(s1.size(), ' '); // 초기화
string s3; // 초기화
s3 = s2; // 할당

vector<string> v; // 초기화
// 1. 복사 생성자로 리턴 값을 일시적으로 복사함
// 2. 할당 연산자로 복사한 값을 할당함
v = split(line);
```

1. 초깃값을 설정하려고 `=`를 사용할 때는 복사 생성자를 호출한다. 이전 값을 제거하지 않는다.
	- 변수의 선언문
	- 생성자로 초기화
	- 함수의 매개변수
	- 함수의 반환 값
2. 할당 표현식에서 `=`를 사용할 때는 `operator=` 함수를 호출한다. 이전 값을 제거한다.

#### 소멸자
객체가 소멸할 때 발생하는 일을 제어함. 이름은 `~`로 시작함.

#### 기본 동작
복사 생성자, 할당 연산자, 소멸자를 명시적으로 정의하지 않은 경우 컴파일러는 기본 버전을 실행함. 기본 버전에서는 개별 요소의 타입에 따라 재귀적으로 동작함.

1. 클래스 타입: 해당 멤버의 복사/할당/소멸자를 호출함.
2. 기본 타입: 값 복사/할당함. 소멸자는 아무런 역할을 하지 않음. 특히 포인터를 소멸시켜도 저장 공간이 해제되지 않음.

클래스가 명시적으로 정의하는 생성자가 있다면(ex. 복사 생성자) 컴파일러가 해당 클래스의 기본 생성자를 실행하지 않음. 따라서 명시적/암묵적으로 기본 생성자를 제공하는 것이 좋음.

#### 3의 법칙 (빅3의 법칙)
복사 생성자, 소멸자, 할당 연산자가 밀접하게 연관된 관계. 클래스가 소멸자를 필요로 할 때는 복사 생성자와 할당 연산자도 필요하다는 의미.

## 11.5
#### (Vec 클래스에서) new, delete를 사용하지 않은 이유
1. 배열을 할당할 때  `new` 연산자를 쓰면 `T`타입에 관한 기본 생성자가 필요한데, `T`타입에 대한 기본 생성자가 없을 수도 있다. (ex. 기본 타입) `new`를 사용하면 표준 벡터만큼의 유연성을 제공하기 어렵다.
2. 초기화가 두 번 일어난다. 배열에서 `new`를 사용하면 모든 요소에 대해 생성자가 불려지고, `Vec` 클래스에 정의된 값으로 초기화가 한 번 더 일어난다.
3. `Vec`의 크기를 두 배로 늘릴 때, `new`는 늘어난 모든 공간을 초기화할 것이지만, 실제로는 사용하는 요소만 초기화하면 된다.

#### C++ 기본 영역(core language)과 메모리 할당
최근 컴퓨터에서는 메모리의 특성이 매우 다양하다. (메모리 속도, 그래픽 버퍼, 공유 메모리 등) 프로그래머가 어떤 종류의 메모리에 데이터를 할당할지 알 수 없으므로, 기본 영역에서는 메모리 할당이라는 개념을 자체 제공하지 않는다. 메모리 할당은 라이브러리에 맡긴다. (입출력을 라이브러리로 구현한 것과 마찬가지다.)

#### allocator
```cpp
#include <memory>
template<class T> class alloactor {
  public:
    T* allocate(size_t);
    void deallocate(T*, size_t);
    void construct(T*, const T&);
    void destroy(T*);
}
```

- `allocate()` : 초기화되지 않은 저장 공간을 할당
- `deallocate()`: 초기화되지 않은 저장 공간을 해제
- `construct()`: 초기화되지 않은 공간에 객체 하나를 만듦, 인수로 저장 공간을 가리키는 포인터와 복사할 값을 전달
- `destroy()`: 소멸자를 실행하여 저장 공간을 다시 초기화되지 않은 상태로 만듦.

```cpp
#include <memory>
//allocator 클래스 멤버 아님
template<class In, class For> For uninitialized_copy(In, In, For);
template<class For, class T> void uninitialized_fill(For, For, const T&);
```

할당해 놓은 저장 공간에 새 객체들을 만들고 초기화함. `In`은 입력 반복자, `For`는 (포인터인) 순방향 반복자.

> Q. 새로운 객체를 만드는 것은 단순히 객체를 할당하는 것 이상의 과정이 존재하므로 For는 출력 반복자가 아닌 순방향 반복자여야 합니다.

- `uninitialized_copy()`: 처음 2개 인수가 나타내는 배열 값을 세 번째 인수가 가리키는 배열로 복사함
- `uninitialized_fill()`: 세 첫 2개의 인수로 주어진 저장 공간을 세 번째 인수 값으로 채움

#### 클래스 불변성(class invarinat)
> ex. data는 첫 번째 요소를 가리키며, 없으면 0. data <= avail <= limit. 요소들은 [data,avail) 범위 안에 만들어짐.

루프 불변상과 마찬가지로, 클래스 객체를 만든 직후부터 모든 멤버 함수가 만족시키는 조건. `public` 멤버들은 클래스 불변성을 거짓으로 만들 수 있는 권한이 없음.

## 연습문제
### 11-1
(값을 복사하는) 기본 버전을 실행하기 위해

### 11-2
클래스가 명시적으로 정의하는 생성자가 있을 때는 컴파일러가 기본 생성자를 실행하지 않는다. 따라서 명시적으로 기본 생성자를 제공해야 한다.

### 11-3
각 멤버와 클래스 값을 각각 할당한다.

### 11-4
기본 타입은 각 데이터 요소를 소멸시키는데, n, midterm, final, homework 중에서 클래스 타입인 `string`과 `vector` 2개만 소멸자가 있다.

### 11-9
growth factor https://en.wikipedia.org/wiki/Dynamic_array