# Chapter 4

## 4.1

**parameter vs. argument**

- parameter: 매개변수. 함수 정의할 때 들어 있는 목록.
- argument: 인수. 함수를 호출할 때마다 매개변수를 초기화하는데 쓰이는 값.

**예외 처리**
`<stdexcept>`헤더에 `domain_error`와 같은 에러들이 정의되어 있다.

```cpp
try {
  streamsize prec = cout.precision();
  cout << "Your final grade is " << setprecision(3)
    << grade(midterm, final, homework) << setprecision(prec);
}
```

출력이 한 줄에 있지만, 이 코드의 `grade()`에서 예외가 발생하면 출력에 잘못된 구문이 포함될 수 있다. grade는 'your final grade is'를 출력한 다음에 호출된다. 또한 두 번째 `setprecision(prec)`이 실행되지 않을 수 있다.

따라서 다음과 같이 수정한다.

```cpp
try {
  double final_grade = grade(midterm, final, homework);
  streamsize prec = cout.precision();
  cout << "Your final grade is " << setprecision(3)
    << final_grade << setprecision(prec);
}
```

**lvalue**
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
