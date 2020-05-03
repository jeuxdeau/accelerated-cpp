# Chapter 14

#### 인터페이스 클래스
- 세부 구현은 다른 클래스에 맡김
- 해당 클래스는 포인터처럼 동작
- 해당 클래스는 기저 메모리를 관리함
- 상속 구조의 인터페이스를 구성할 때 사용

#### 객체 복사
객체의 복사본이란, 기존 객체의 모든 속성을 갖는 별개의 객체이다. 객체 x가 객체 y를 참고할 때, x를 복사하면 y도 복사되어야 할까?
1. y가 x의 멤버라면, x를 복사하면 y도 복사되어야 한다.
2. x가 y를 가리키는 포인터라면, y는 복사되지 않아야 한다.

## 14.1

#### 포인터 실수
1. 포인터를 복사해도 포인터가 가리키는 객체는 복사되지 않는다.
2. 포인터를 소멸시켜도 포인터가 가리키는 객체는 소멸되지 않는다.
3. 포인터를 소멸시키지 않고 포인터가 가리키는 객체만 소멸시키면 dangling Pointer undefined behavior 발생.
4. 포인터를 만들 때 초기화하지 않고 사용하면 undefined behavior 발생.

#### 제네릭 핸들 클래스
프로그래머는 모든 객체에 단 하나의 Handle 객체를 바인딩하고, 포인터를 이용해 직접 접근하지 않는다. 기본 포인터에서의 문제를 해결하기 위해 Handle 객체를 복사할 대는 바인딩된 객체의 복사본을 만들고, Handle 객체를 소멸시킬 때는 객체도 소멸시킨다.

```cpp
template <class T> Handle<T>& Handle<T>::operator=(const Handle& rhs) {
	if (&rhs != this) {
		delete p;
		p = rhs.p ? rhs.p->clone() : 0;
	}
	return *this;
}
```

## 14.2

#### 참조 카운트
얼마나 많은 객체가 또 다른 객체를 참고하는지 추적하는 객체 `Ref_hanldle`. 서로 다른 객체 복사본끼리 상태를 공유하고 싶을 때 사용한다.

```cpp
template <class T> Ref_handle<T>& Ref_handle<T>::operator=(const Ref_handle& rhs) {
  ++*rhs.refptr; // 자가 할당 방지
  if (--*refptr == 0) {
    delete refptr;
    delete p;
  }

  refptr = rhs.refptr;
  p = rhs.p;
  return *this;
}
```
`clone()`을 호출하지 않아 바인딩된 객체를 복사하지 않는다. 불필요한 데이터의 복사를 피할 수 있지만, 필요하든 불필요하든 무조건 모든 복사를 피한다.

```cpp
template <class T> Ref_handle<T>::~Ref_handle() {
  if (--*refptr == 0) {
    delete refptr;
    delete p;
  }
}
```

## 14.3

#### 데이터 공유 여부를 결정하는 핸들 클래스
`Ptr` 클래스는 객체를 복사할 때와 복사하지 않을 때를 결정한다. 프로그래머가 어떤 객체의 내용을 바꾸려고 할 때, 이 객체에 또 다른 핸들 객체가 바인딩되었을 때만 객체를 복사한다. 유일하게 바인딩되었는지 여부는 참조 카운트로 판단한다.

```cpp
void make_unique() {
  if (*refptr != 1) {
    --*refptr;
    refptr = new size_t(1);
    p = p ? p->clone() : 0;
  }
}
```

`make_unique()`를 제외하면 `Ref_handle` 클래스와 같다. 다음과 같이 사용한다.

```cpp
void do_something() {
  cp.make_unique();
  if (cp)
    cp->modify();
    
  else
    throw run_time_error("error message");
}
```

## 14.4
`Ptr::make_unique()`에서, `p->clone()`이 존재하지 않고, `p` 클래스의 정의문을 바꿀 수 없을 수 있다면 어떻게 할까? **모든 문제는 간접 접근 단계를 도입해서 해결할 수 있다.**

#### 템플릿 특수화
템플릿 특수화는 특정 인수 타입이 있는 템플릿 함수의 특수화된 버전을 정의한다.

```cpp
template<class T> T* clone(const T* tp) {
  return tp->clone();
}

template<> P* clone(const P* p) {
  return new P(*p);
}
```

템플릿 특수화 이후 `Ptr::make_unique()`의 `p->clone()`을 `clone(p)`로 바꾸면 의도한 대로 동작한다. `clone` 함수에 `P*` 타입의 인수를 전달하면 컴파일러는 특수화된 버전을 사용하고, 다른 타입을 전달하면 일반적인 템플릿 형태를 인스턴스화한다.
