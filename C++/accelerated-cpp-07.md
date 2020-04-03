# Chapter 7

## 7.1

#### 연관 컨테이너 (associative container)

- 키(key): 탐색에 사용할 수 있는 요소의 일부분
- 인덱스와의 차이점: 삽입, 삭제 시 인덱스는 변함
- 연관 배열: (키, 값) 쌍을 저장하고 키를 기반으로 탐색 ex. 맵
- 연관 컨테이너는 자동으로 요소를 정렬하기 때문에, 요소 순서를 직접 바꿀 수 없음. 즉, 컨테이너 내용을 변경하는 것이 불가능함.

## 7.2

#### 값 초기화 (value-initialize)

```cpp
map<string, int> counters;
++counter["abc"]; // 0으로 값 초기화
```

아직 존재하지 않는 키로 맵에 접근할 때, 해당 키를 갖는 새로운 요소가 자동 생성됨. 이를 값 초기화라고 함. 여기서는 `int` 타입이므로 0으로 초기화되었음.

#### pair

```cpp
for (map<string, int>::const_iterator it = counters.begin();
  it != counters.end(); ++it) {
    cout << it->first << "\t" << it->second << endl;
  }
```

`pair` 타입은 `first` 요소로 키와 `second` 요소로 값을 가짐. 맵이 `K` 타입의 키와 `V` 타입의 값이 있다면, 해당 `pair` 타입의 형태는 `pair<const K, V>`이다. 키의 타입은 `const` 이므로 키 값은 바꿀 수 없다. 만약 키가 `const`가 아니라면, `*it`이 lvalue이기 때문에(따라서 `first`, `second`도 lvalue), 맵에서 요소 위치를 바꿀 수 있다.

## 7.3

#### 기본 인수 (default argument)

매개변수에 기본 인수를 지정하면 함수를 호출할 때 해당 인수를 생략할 수 있다. 함수 호출 시 인수를 전달하면 전달한 인수를 사용하고, 생략하면 컴파일러가 기본 인수를 사용한다.

```cpp
int foo(double d) {...};
int bar(int baz(double) = foo);
```

## 7.4

```cpp
const map<string, int> m;
int::const_iterator it = m.find(key); // GOOD
int::const_iterator it = m[key]; // BAD
```

1. 존재하지 않는 키로 맵에 접근하면(`m[key]`) 해당 키 요소를 자동으로 만든다.
2. `find`는 반복자를 반환하거나 해당 요소가 없을 때는 `m.end()`를 반환한다.
3. `[]`문법은 `const` 맵에서 지원하지 않는다.

#### rand()
```cpp
#include <cstdlib>
int r = rand();
```
`rand()`는 [0, `RAND_MAX`] 범위에서 임의의 정수를 반환하다.

```cpp
int bad_nrand = rand() % n;
int nrand(int n) {
  const int bucket_size = RAND_MAX / n;
  int r;
  do r = rand() / bucket_size; // do while문
  while (r >= n);

  return r;
}
```
[0, n) 범위의 정수를 반환하고 싶을 때, `rand() % n`과 같은 방식으로 하면 안된다. `rand()`는 pseudo-random이기 때문에 나머지가 임의적이지 않다.

## 7.6

#### 값 초기화
맵에서 존재하지 않는 V타입 요소에 접근하면 값이 `V()`인 요소를 만든다. 기본 타입은 0으로 초기화된다.