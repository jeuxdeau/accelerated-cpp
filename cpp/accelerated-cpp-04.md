# Chapter 4

## 4.1

#### parameter vs. argument

- parameter: 매개변수. 함수 정의할 때 들어 있는 목록.
- argument: 인수. 함수를 호출할 때마다 매개변수를 초기화하는데 쓰이는 값.

#### 예외 처리

`<stdexcept>`헤더에 `domain_error`와 같은 에러들이 정의되어 있다.

```cpp
try {
  streamsize prec = cout.precision();
  cout << "Your final grade is " << setprecision(3)
  ####  << grade(midterm, final, homework) << setprecision(prec);
}
```

출력이 한 줄에 있지만, 이 코드의 `grade()`에서 예외가 발생하면 출력에 잘못된 구문이 포함될 수 있다. grade는 'your final grade is'를 출력한 다음에 호출된다. 또한 두 번째 `setprecision(prec)`이 실행되지 않을 수 있다.

따라서 다음과 같이 수정한다.

```cpp
try {
  double final_grade = grade(midterm, final, homework);
  streamsize prec = cout.precision();
  cout << "Your final grade is " << setprecision(3)
    << final_grade << setprecision(prec) << endl;
}
catch (domain_error e) {
  cout << e.what(); // 해당 에러의 내용을 복사
  return 1;
}
```

#### lvalue

비일시적 개체를 나타내는 값

```cpp
istream& read_hw(istream& in, vector<double>& hw) {
  if (in) {
    hw.clear(); // 벡터를 초기화함
    double x;

    while (in >> x) {
      hw.push_back(x);
    }

    in.clear();
    // while (in >> x)에서, EOF를 만났거나 다른 타입을 만나는 등
    // 입력이 종료되는 상황이 되면 입력 스트림은 실패 상태(failure state)가 된다.
    // 이때 다음번 read_hw 함수를 실행시킬 때는 무조건 while (in >> x)
    //
  }
}

if (read_hw(cin, homework));
```

## 4.3

#### 개별 컴파일

프로그램을 개별 파일에 저장하고, 각 파일을 독립적으로 컴파일함

#### 헤더 파일

- 다른 프로그래머가 내가 정의한 함수를 사용할 수 있게 함.
- `include "header.h"`와 같이 쓰면 헤더 파일 내용 전체가 복사됨.
- `using` 선언을 하면 해당 헤더를 포함하는 모든 프로그램에 같은 `using` 선언이 포함됨
- 따라서 헤더 파일에는 네임스페이스와 범위가 있는 이름 사용

> cf. 인라인 함수는 일반적으로 헤더 파일에 정의한다.

```cpp
#ifndef GUARD_median_h
#define GUARD_median_h

/* median.h 내용*/

#endif
```

헤더파일이 두 번 이상 포함되는지 확인한다. `#ifndef`는 주어진 이름이 정의되어 있지 않으면 `#endif`를 만나기 전까지 모든 것을 처리한다. 보통 파일의 맨 첫 행에 적는다.

## 4.4

헤더에 `double f(double)`이라고 정의되어 있을 때, 내가 `int f(double)`을 쓰면 리턴 타입이 맞지 않아 컴파일러가 경고함. 하지만 `double f(int)`를 쓰면 오버로드된 것이라고 생각하고 경고하지 않음.

## 4.6

#### 라이브러리 기능

- `s.width(n)`: 다음 출력때 스트림 s의 너비를 n으로 설정한다. n을 생략하면 너비를 그대로 둔다. 출력은 왼쪽부터 주어진 너비만큼 공백을 채워서 출력된다. 리턴값은 이전 너비이다. 표준 출력 연산자는 기존 너비 값을 사용한 후 `width(0)`을 호출한다.
- `setw(n)`: `s.width(n)`을 호출한 효과가 있는 `streamsize` 타입을 리턴한다.
