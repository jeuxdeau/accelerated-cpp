# Chapter 8
## 8-6
1) map<int, string> 반복자를 dereference 하면 나오는 것은 pair<const int, string>이므로, pair<const int, string>의 iterator일 것. 또한 back_inserter()는 push_back()을 지원해야 한다.

2) back_inserter()는 push_back()을 지원하는 컨테이너에서만 사용할 수 있기 때문에 copy(x.begin(), x.end(), back_inserter(m))는 할 수 없다.

## 8-7
(나의 생각) 다른 타입끼리 비교시키면 >= 연산자를 정의해야 함 (ex. int >= string)
(팀원 분들의 생각) 리턴 타입을 뭘로 할지 모름

## 8-8
1) 오버플로우 문제 https://stackoverflow.com/questions/38688028/why-prefer-start-end-start-2-over-start-end-2-when-calculating-the

2) begin, end가 모두 iterator일 때 +하는 연산이 정의되지 않음



