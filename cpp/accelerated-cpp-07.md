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

s