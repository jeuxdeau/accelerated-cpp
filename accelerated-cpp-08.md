# Chapter 8

## 8-1.

#### 제네릭 함수
함수를 사용할 때 인수와 반환 값의 타입이 정해지는 것. 함수가 매개변수로 어떤 연산을 하는지가 그 매개변수의 타입을 제한한다. 예를 들어 매개변수 `x`로 `x + x`를 계산한다면, `x`는 `+`를 정의하는 타입임을 암묵적으로 나타낸다.

#### 템플릿 함수
프로그래밍 언어 차원에서의 제네릭 함수 구현.

```cpp
template<class T>
typedef typename vector<T>::size_type vec_size;
```

구현체는 `vector<T>::size_type`이 타입인지 아닌지 알 수 없는 상태이므로, `typename`을 써서 이것이 타입 이름이라는 것을 알려준다.

#### 템플릿 인스턴스화

타입은 컴파일 과정에서 함수를 호출하는 시점에 결정된다. 구현체는 해당 함수의 특정 버전을 작성한 것처럼 코드를 인스턴스화한다. C++ 표준에서는 구현체가 어떻게 템플릿 인스턴스화를 할지 규정하지 않으므로 모든 구현체는 자체 방법으로 처리한다. 일반적인 C++ 구현체에서는 컴파일 과정이 아닌 링크 과정에서 인스턴스화가 일어난다. 따라서 링크 과정에서 컴파일 오류가 발생할 수 있다.

## 8.2
```cpp
find(c.begin(), c.end(), val);
c.find(val);
find(c, val);
```

1. 컨테이너의 종류와 관계없이 동작하기 만들기 위해
2. 컨테이너에 멤버 함수를 정의하지 않고도 사용할 수 있게 하기 위해
3. 컨테이너에 포함되지 않는(ex. 역순 접근) 요소에 접근하기 위해

#### 반복자 카테고리

일반적으로 나중 카테고리는 이전 카테고리의 연산을 포함한다.

1. 입력 반복자: 한 방향 순차 접근. 입력 전용.
2. 출력 반복자: 한 방향 순차 접근. 출력 전용. 값을 두 번 이상 할당하거나, 증가를 두 번 이상 하면 안됨. ex. `back_inserter` 이는 반복자의 스펙이 아니라 이 반복자를 사용하는 프로그램에서 지켜야 하는 것임.
3. 순방향 반복자: 한 방향 순차 접근. 입출력 모두.
4. 양방향 반복자 양 방향 순차 접근. 입출력 모두.
5. 임의 접근 반복자: 임의 접근 가능. 입출력 모두. `sort`에서 유일하게 사용한다. 벡터, 문자열의 반복자는 임의 접근이고 리스트는 아니다.

### 범위 끝

범위 끝은 마지막 요소 다음으로 나타낸다. 이것은 정의되지 않은 상태이다. 마지막 요소가 아닌 다음 요소인 이유는,

1. 비어있을 때 범위 끝을 표시할 요소가 없어서
2. 다 탐색했을 때 서로 일치하는지만 판단하면 돼서
3. 범위를 벗어난 상태를 자연스럽게 표현하기 위해서

## 8.3

#### 입력 반복자

```cpp
vector<int> v;
// 표준 입력에서 int를 읽어 v에 추가
copy(istream_iterator<int>(cin), istream_iterator<int>(), back_inserter(v));
```

- C++ 입출력은 타입이 지정되어야 한다. 여기서는 `int`다.
- `copy`의 두 번째 인수는 어떤 기본 `istream_iterator<int>`이다. 기본값은 EOF에 도달하거나 오류 상태일 때와 같은 값이다.

#### 출력 반복자

```cpp
copy(v.begin(), v.end(), ostream_iterator<int>(cout, " "));
```

- 세 번째 인수는 `int` 타입 값을 출력하고자 cout에 바인딩된 반복자를 생성한다.
- `" "`는 각 요소를 출력하고 다음에 출력할 값이다. 문자열이 아닌 문자열 리터럴이어야 한다.

## 연습문제

### 8-6

1. map<int, string> 반복자를 dereference 하면 나오는 것은 pair<const int, string>이므로, pair<const int, string>의 컨테이너일 것. 또한 back_inserter()는 push_back()을 지원해야 한다.

2. back_inserter()는 push_back()을 지원하는 컨테이너에서만 사용할 수 있기 때문에 copy(x.begin(), x.end(), back_inserter(m))는 할 수 없다.

### 8-7

(나의 생각) 다른 타입끼리 비교시키면 >= 연산자를 정의해야 함 (ex. int >= string)
(팀원 분들의 생각) 리턴 타입을 무엇으로 할지 알 수 없기 때문

### 8-8

1. begin, end가 모두 iterator일 때는 + 연산이 정의되지 않음
2. 숫자 begin + end에서 오는 [오버플로우 문제](https://stackoverflow.com/questions/38688028/why-prefer-start-end-start-2-over-start-end-2-when-calculating-the)를 방지함