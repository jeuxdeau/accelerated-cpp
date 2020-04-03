# Chapter 6

## 6.1

```cpp
copy(begin, in, out);
while (begin != end)
  **out++ = *begin++;
copy(v1.begin(), v1.end(), back_inserter(v2));
copy(v1.begin(), v1.end(), v2.end()); // v2.end()에는 어떤 요소도 들어있지 않으므로 구현체에 따라 어떤 동작을 할지 예측할 수 없다.
```

- 제네릭 알고리즘: `copy`와 같이 특정 종류의 컨테이너에 속하지 않고 인수의 타입으로 데이터에 접근하는 알고리즘.
- 반복자 어댑터: 인수와 관련된 속성이 있는 반복자를 반환하는 함수. `<iterator>`헤더에 정의되어 있다. `back_inserter()` 함수는 컨테이너를 인수로 받아 목적지인 반복자를 반환한다.

```cpp
bool space(char c) {
  return isspace(c);
}

// find_if()의 첫 두 인수는 순차열을 나타내는 반복자들
// 세 번째 인수는 참 또는 거짓을 반환함
const_iterator i = find_if(s.begin(), s.end(), space);
```

`isspace()` 함수는 오버로드 되었으므로, `wchar_t`같은 타입으로 동작할 수도 있다. 원하는 함수 형태를 컴파일러에 명시하기 위해 `space()`함수를 직접 작성한다.

```cpp
#include <algorithm>
static const string s = "abc";

// find_if와 다르게 find는 특정 값 c를 찾는다.
find(s.begin(), s.end(), c);

// s1을 탐색해서 s2를 찾는다.
search(s1.begin(), s1.end(), s2.begin(), s2.end());
```

`static` 지역 변수를 선언하면 몇 번을 호출해도 첫 번째 호출에서 만든 객체를 사용한다.

```cpp
const_iterator iter;

iter[-1]; // iter 바로 전 요소
*(iter - 1);

iter[s.size()]; // iter에서 s.size() 떨어진 요소
*(i + s.size());
```

컨테이너가 인덱스를 지원하면 반복자도 인덱스를 지원한다.

## 6.2

#### transform()

```cpp
#include <algorithm>
transform(v1.begin(), v1.end(), back_inserter(v2), func);
```

`v1.begin()`부터 `v1.end()`까지 각 요소에 `func`를 적용하고 `back_inserter` 자리에 삽입한다.

- 첫 두 개의 반복자: 적용할 범위
- 세 번째 반복자: 적용한 뒤 저장할 위치
- 네 번째 인수: 저장되기 전 거쳐야 할 함수.
  - 함수가 오버로드 됐을 경우, 컴파일러는 호출할 버전을 알지 못함을 주의.

#### 함수 매개변수

```cpp
double func1(double func2(const vector<int>&));
```

함수 선언과 같은 형태로, 함수를 매개변수로 전달할 수 있다.

#### accumulate()

```cpp
#include <numeric>
accumulate(v.begin(), v.end(), 0.0);
```

- 첫 2개 인수 범위 값들을 더해서, 세 번째 인수에 더한다.
- 세 번째 인수의 타입에 따라 합의 타입이 결정된다. 0.0 대신 0을 넣으면 결과가 int 타입이 되어 소수 부분이 손실된다.

#### remove_copy()

```cpp
#include <algorithm>
copy(v1.begin(), v1.end(), back_inserter(v2));
remove_copy(v1.begin(), v1.end(), back_inserter(v2), val);
remove_copy_if(v1.begin(), v1.end(), back_inserter(v2), func);
```

v1에서 val, func에 해당하는 값을 제거한다. (v1에 그대로 남긴다.) 해당하지 않는 값들은 v2에 복사한다.

- 첫 두 개의 반복자: 범위
- 세 번째 반복자: 저장할 위치
- 네 번째 인수: 값 또는 서술 함수

```cpp
#include <algorithm>
remove(v1.begin(), v1.end(), val);
remove_if(v1.begin(), v1.end(), func);
```

일치하는 모든 값을 찾아 컨테이너에서 제거하고, 제거하지 않은 모든 값을 지정한 위치로 복사한다. '제거'의 의미는 실제로 요소를 제거하는 것이 아니라, 제거되는 요소들을 컨테이너의 처음 위치부터 덮어 씌운다는 이야기다.

> 원래 요소가 v[p f p f p] 였다고 하자. remove(v1.begin(), v1.end(), p)를 호출한다면, 결과는 v[p p p f p]이고 f를 가리키는 반복자를 반환한다. 즉 처음 세 개만 제거됐다는 것을 알리는 것이다.

## 6.3

#### erase()

```cpp
#include <vector>
v.erase(iter1, iter2);
```

[iter1, iter2) 범위의 모든 요소를 지운다.

#### partition(), stable_partition()

```cpp
#include <algorithm>
partition(v.begin(), v.end(), func);
stable_partition(v.begin(), v.end(), func);
```

요소들을 카테고리로 구분해서 재배치한다. `stable_partition`은 원래 순서를 유지한다. 리턴값은 두 번째 카테고리의 첫 번째 요소를 나타내는 반복자이다.

> 원래 요소가 v[p f p f p] 이고 partition(v.begin(), v.end(), is_p)를 호출한다면, 결과는 v[p p p f f]이고 첫 f를 가리키는 반복자를 반환한다.

## 6.4

#### 알고리즘은 컨테이너의 요소만을 다루고, 컨테이너 자체를 다루지 않는다.

- `remove_if()`는 벡터 크기를 바꾸지 않는다.
- `erase()`는 컨테이너를 직접 다루므로 벡터의 멤버 변수여야 한다.
- 벡터나 문자열은 `erase()`나 `insert()` 연산한 요소 이후의 반복자를 모두 무효로 만든다.
- `partition()`이나 `remove_if()`도 특정 반복자가 나타내는 요소를 바꾸므로, 특정 요소를 계속해서 나타내는 반복자를 쓸 수 없다.

## 6.5

#### 반복자 어댑터

`<iterator>` 헤더에 정의되어 있다.

- `back_inserter(c)`: c 뒤에 요소 추가. 컨테이너가 `push_back()` 지원해야.
- `front_inserter(c)`: c 앞에 요소 추가. 컨테이너가 `push_front()` 지원해야.
- `inserter(c, it)`: 반복자 `it` 앞에 요소 삽입
