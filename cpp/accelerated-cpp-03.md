# Chapter 3

## 3.1

- `<ios>`: `streamsize` 타입 정의. 입출력시 문자 수나 버퍼 크기를 나타냄.
- `<iomanip>`: 출력 결과의 유효 자릿수를 나타내는 `setprecision()` 정의. `cout.precision()`으로 같은 일을 할 수 있음.
- `cout.precision()`: 현재 스트림이 사용하는 정밀도를 알려줌. 다음 출력이 없을 때 정밀도를 원상복귀하는 것이 좋은 습관임.

> 유닉스 계열에서는 [ctrl] + [d]키로 EOF 신호를 보낼 수 있다.

**double만 사용하자**

- float: 4바이트 정밀도 부동 소수점. 정밀도 6자리
- double: 8바이트 정밀도 부동 소수점. 정밀도 10자리

과거에는 메모리 가격 때문에 float를 사용했으나, 현재는 연산 속도에 차이가 없거나 double이 더 빠름

```cpp
cout << "공백으로 분리된 2개 이상의 문자열 리터럴은 "
 "이와 같이 자동으로 결합된다.";
```

**초기화**

```cpp
double sum; // 기본 초기화. 기본 타입 지역 변수는 암묵적인 초기화를 하지 않음.
// undefined 상태의 의미 없는 값이고, 할당문에서 왼쪽에만 올 수 있다.
string str; // 클래스 자체에서 정한 초기화 방식을 따르기도 함.
double sum1 = 0.0;
double sum2 = 0; // 컴파일 중에 double 타입으로 변환을 실행하므로, 런타임 오버헤드가 발생하지 않음.
```

**if(cin)의 의미**

istream 클래스에서는 cin 역시 숫자로 바꿀 수 있다. 조건문에서 cin은 cin을 이용해 최근 입력을 받았는지 판별하는 것이다.

1. EOF에 도달했을 때
2. 타입에 맞지 않는 입력을 받았을 때
3. 시스템이 입력 장치에서 하드웨어 장애를 감지했을 때

위의 세 가지 경우에 `cin` 조건이 거짓이 된다.

**unsigned int**

```cpp
typedef vector<double>::size_type vec_sz;
vec_sz size = vec.size();
```

`size - 100`의 결과는 어떤 경우에도 양수다. unsigned int와 signed int가 하나의 표현식에 섞여 있을 때는 signed int가 unsigned int로 바뀌기 때문이다.

