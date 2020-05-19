# delete와 delete[]

> TL;DR: new[]로 할당한 것을 delete(delete[]가 아닌)로 해제하는 것은 C++ 표준에 **정의되지 않은(undefined)** 동작이다. 정의되지 않은 것이 어떻게 동작하는가는 구현체마다 다를 뿐, 무슨 일이 일어나더라도 이상하지 않다.

## Deep dive
아래의 내용은 C++ 표준이 아니라 특정 구현일 뿐이다. 기준은 `g++ 9.2.1` 이다.

### Q. new[]로 할당한 배열을 delete로 지우면 delete[]와 같은 동작을 하는가?
1. 짧은 답: 그럴 수도 있고 아닐 수도 있다.
2. 긴 답:

### 기본 타입

민님이 스터디에 가져온 예제. 배열에 값을 넣은 뒤, `delete`한 결과와 `delete[]`하고 출력한 결과가 같아서 같은 동작을 할 것이라고 막연히 추측했다.

```cpp
#include <iostream>
using namespace std;
 
int main(void) {
  int *p = new int[3];
  // p[0] = p[1] = p[2] = 1;
  delete p;
  //delete[] p;
  //cout << p[0] << ' ' << p[1] << ' ' << p[2] << endl;
}
```

정말로 같은 일이 일어나는가를 알아보기 위해 disassemble 해본다. 똑같이 `_ZdlPv`를 호출하는 것을 확인했다.

**delete p**
```
00000000004049b0 <_ZdlPvm>:
4049b0: e9 1b f9 07 00        jmpq   4842d0 <_ZdlPv>
4049b5: 66 2e 0f 1f 84 00 00  nopw   %cs:0x0(%rax,%rax,1)
4049bc: 00 00 00 
4049bf: 90                    nop
```

**delete p[]**
```
00000000004049b0 <_ZdaPv>:
4049b0: e9 1b f9 07 00        jmpq   4842d0 <_ZdlPv>
4049b5: 66 2e 0f 1f 84 00 00  nopw   %cs:0x0(%rax,%rax,1)
4049bc: 00 00 00 
4049bf: 90
```

### 클래스 타입

constructor와 destructor가 있는 custom type 객체를 생성해 보고, `delete`와 `delete[]`를 했을 때 각각 무슨 일이 일어나는지 비교해 본다.

```cpp
#include <iostream>
using namespace std;

class Foo {
 public:
    static int count;
    int index;
    
    Foo() {
        index = ++count;
        cout << "Constructing Foo " << index << endl;
    }
    
    ~Foo() {\
        --count;
        cout << "Destructing Foo " << index << endl;
    }
};

int Foo::count = 0;
 
int main(){
    Foo *ptr = new Foo[3]; // constructs 1... 2... 3...

    delete ptr; // destructs 1, invalid pointer
    // delete[] ptr; // destructs 3... 2... 1...

    return 0;
}
```

1. construct의 결과로 index 1, 2, 3을 갖는 `Foo` 객체 3개가 만들어진다.
2. `delete ptr[]`을 실행하면 책에 나온 대로 3, 2, 1번 역순으로 destruct된다.
3. `delete ptr`을 하면 1번이 destruct 되고, 에러가 발생한다.

### new와 delete

이것은 `new`와 `delete`의 내부 동작 방식 때문이다.

`new`로 할당한 메모리를 해제할 때, 우리는 `delete` 또는 `delete[]`에 메모리의 크기나 배열의 길이 등을 넘겨주지 않는다. 
컴파일러는 어떻게 얼마만큼의 메모리를 해제해야 하는지, 또는 destructor를 몇 번이나 호출해야 하는지 알 수 있을까? 일반적으로 여기에는 두 가지 방법이 있다.

1. [over-allocation](https://isocpp.org/wiki/faq/compiler-dependencies#num-elems-in-new-array-overalloc): 메모리를 새로 할당할 때, 실제로 할당할 메모리 블록 앞쪽에 메모리의 크기나 배열의 길이 같은 메타데이터를 저장한다. 즉, 프로그래머가 요구한 공간보다 더 큰 공간을 사용한다.
2. [associative array](https://isocpp.org/wiki/faq/compiler-dependencies#num-elems-in-new-array-assocarray): 전역(global) associative array를 선언하고, 메모리를 할당할 때마다 associative array에 할당 정보를 넣는다.

보통은 1번 방법을 쓴다.

### over-allocation
over-allocation 방식으로 할당된 배열 `ba[4]` 메모리의 모습을 그림으로 살펴보자.

![allocated_memory](http://web.archive.org/web/20080703153358im_/http://i144.photobucket.com/albums/r180/taossa/arraymem1.jpg)

그림의 모든 것을 이해할 필요는 없다. 여기서 이해해야 할 것은 배열이 저장될 때 실제 배열 공간 앞 4바이트에 `count`(배열의 길이)가 저장되고, `delete[]`를 호출할 때 컴파일러가 이 `count`를 읽는다는 것이다. 따라서 컴파일러는 배열의 길이만큼 destructor를 호출할 수 있다.

그러면 배열이 아닌 포인터에 `delete[]`를 호출했을 때 어떤 일이 일어날지는 알기 쉽다.

![delete non array](http://web.archive.org/web/20080703153358im_/http://i144.photobucket.com/albums/r180/taossa/arraymem2.jpg)

만약 프로그래머가 `ba[2]`를 `delete[]` 한다면, 컴파일러는 4바이트 앞에 있는 664를 `count`로 오해하고 잘못된 동작을 실행할 것이다.

### malloc() and free()
반대로 `ba[0]`에 `delete[]`가 아닌 `delete`를 호출하면 어떤 일이 일어날까? 먼저 생각해볼 수 있는 문제는 `delete[]`를 호출할 때처럼 `count`를 읽지 않기 때문에 `ba[0]`을 제외한 나머지 `ba[1]`, `ba[2]`, ..., 등이 destruct되지 않는다는 것이다.

두 번째 문제는 위의 문제와 마찬가지로 잘못된 메타데이터를 읽어서 생긴다. 효율적인 `free()`를 위해 컴파일러는 malloc chunk header에 여러 가지 데이터를 저장하는데, 이전 블록 사이즈, 현재 블록 사이즈, 이전 블록이 할당되었는지 아닌지 등을 표시해 둔다.

![delete array](http://web.archive.org/web/20080703153358im_/http://i144.photobucket.com/albums/r180/taossa/arraymem3_01.jpg)

`new ba[4]`를 선언하면 프로그래머는 `ba[0]` 포인터를 얻는다. 그런데 아까 살펴본 것처럼 이 배열을 위해 실제 공간이 할당된 것은 `ba[0]` 자리가 아니라 `count`부터 시작한다. 프로그래머가 갖고 있는 포인터를 `delete`로 지우려 한다면, 실제 malloc chunk header가 아닌 `count` 공간을 malloc chunk header로 잘못 읽게 되어 정의되지 않은 동작이 발생한다.

### 그러면 우리는 왜 delete와 delete[]가 똑같은줄 알았나?
primitive type 배열에서는 `count` 변수를 저장할 필요가 없다. constructor나 destructor가 없기 때문이다. 따라서 `delete`와 `delete[]`의 동작도 같다.

TODO:
1. malloc chunk header 내용 보충하기
2. 다 정리하면 finance study doc에 링크 걸기 (이 문서, sizeof)

> ref 1) [디버깅 이야기: malloc, free, new[], delete[]](https://kldp.org/node/1073)  
> ref 2) [Attacking delete and delete [] in C++](http://web.archive.org/web/20080703153358/http://taossa.com/index.php/2007/01/03/attacking-delete-and-delete-in-c)  
> ref 3) [MallocInternals](https://sourceware.org/glibc/wiki/MallocInternals)  
> ref 4) [malloc() 으로 메모리를 할동 받는 작동 원리](https://3dmpengines.tistory.com/1660)  
