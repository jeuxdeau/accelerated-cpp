# Chapter 5

## 5.2

#### 반복자

```cpp
container-type::const_iterator
container-type::iterator
```

모든 표준 컨테이너는 2개의 반복자 타입을 정의한다. `iterator` 타입은 `const_iterator`으로 자동 변환할 수 있다. (ex. `const_iterator iter = vec.begin()`)

연산자 `.`는 `*`보다 우선순위가 높다. `(*iter).name`, 또는`iter->name`과 같이 표기하는 이유다.

```cpp
iter = student.erase(iter);
```

## 5.5

#### 벡터와 반복자

벡터에서 `iter`가 가리키는 요소를 `erase`하면, 요소들이 이동하기 때문에 해당 반복자와 그 다음 요소들을 가리키는 반복자들 모두 무효가 된다. `erase` 함수는 제거된 요소의 다음 요소를 가리키는 반복자를 반환한다. 마찬가지로 `push_back`을 사용하여 요소 하나를 추가해도 해당 벡터를 참조하는 모든 반복자가 무효가 된다. 벡터 공간 전체가 재할당되기 때문이다.

#### 리스트와 반복자

리스트의 반복자는 임의로 접근할 수 없어 STL의 `sort`를 사용할 수 없다. 단 멤버 함수 `sort`가 정의되어 있다.

```cpp
list<type> l;
l.sort();
l.sort(compare);
```

리스트에서 `erase`나 `push_back`을 사용하더라도 다른 요소를 참조하는 반복자는 무효가 되지 않는다. 실제로 제거된 요소를 참조하는 반복자만 무효가 된다.

## 5.6

#### \<cctype\> 헤더

개별 문자를 처리하는 함수들을 정의하는 헤더. `isspace()`는 `char` 타입을 받아 해당 객체가 [whitespace](http://www.cplusplus.com/reference/cctype/isspace/)인지를 나타냄.

- `isalpha(c)`
- `isdigit(c)`
- `isalnum(c)`
- `ispunct(c)`
- `isupper(c)`
- `islower(c)`
- `toupper(c)`
- `tolower(c)`

## 5.6, 5.7

#### \<string\> 라이브러리

- `istream& getline(istream&, string&)`: 행 끝에 도달할 때가지 입력을 읽음. 리턴값이 `istream&`이므로 `if getline(cin, s)`와 같은 식으로 사용할 수 있음.
- `substr(index, length)`: 일 때, [i, i + length) 범위 안 모든 문자를 복사한다.

## 5.8

```cpp
first.insert(first.end(), second.begin(), second.end());
```

## 5.9

#### 컨테이너와 반복자

- `c.rbegin()`, `c.rend()` 컨테이너를 역순으로 접근할 수 있다.
- `container<T> c` 비어 있는 컨테이너를 정의한다.
- `container<T> c(n)` 초기화된 n개 요소가 있는 컨테이너로 정의한다. 기본 산술 타입이면 0으로 초기화된다.
- `container<T> c1(c2)` c2의 복사본으로 c1을 정의한다.
- `container<T> c1(n, c2)` c2의 복사본 n개로 c1을 정의한다.
- `container<T> c(b, e)` [b, e) 범위의 반복자 복사본으로 c를 정의한다.

#### 벡터 타입

- `v.reserve(n)` n개의 저장 공간을 예약하되, 초기화하지 않는다. `insert`나 `push_back`을 반복적으로 호출할 때 사용한다.
- `v.resize(n)` 크기를 n으로 새롭게 지정한다. 만약 현재 크기보다 작으면 n개 이후는 제거한다. 현재 크기보다 크면 초기화된 요소들이 추가된다.
