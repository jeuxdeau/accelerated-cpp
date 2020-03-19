# Chapter 2
## 2.3
**루프 불변성(loop invariant)**
- while문 내부에서 항상 참
- 실제 실행하는 코드가 아니라 주석 형태임

## 2.4
`std::string`의 문자 개수는 `size_type` 타입으로 정의됨.
특정 데이터 구조의 길이를 저장할 때는 `size_type`과 같이 목적에 맞게 정의된 타입을 사용하는 것이 좋음.
> `size_type`은 `size_t`, 즉 unsigned int형임.

## 2.5
`using` 선언은 선언한 지점부터 scope가 끝나기 전까지만 유효함.

## 2.6
비대칭 범위 (ex. `[0, rows)`)의 장점
1. 비어 있는 범위를 나타내기 좋다. (ex. `[0, 0)` vs. `[0, -1]`)
2. 루프 불변성을 쉽게 표현할 수 있다. (ex. 지금까지 n개 행 출력 => n이 1부터 시작한다면?)
3. 비교 연산자로 `<=`대신 `!=` 사용 가능. 즉 마지막에 프로그램의 상태가 `==`임을 표현하기 쉬움.

## 2.7
`x / y`는 구현체에 따라 0 또는 -inf로 반올림함.
> [Directed roundings](https://en.wikipedia.org/wiki/IEEE_754#Directed_roundings) 표 참고

피연산자는 정밀도를 보존하는 쪽으로 변환된다.

**타입의 종류** 
- `short`: 최소 16비트인 int
- `long`: 최소 32비트인 int
- `size_t`: 모든 객체의 크기를 저장할 수 있는 unsigned int

## 연습문제
### 2-10.
`using std::cout` 선언은 블록 안에서만 유효함
