## 10.1 클로저 소개
- 리스프 구문의 기본 단위는 평가할 표현식으로 구성
	- 일반적으로 대괄호로 둘러싸인 0개 이상의 기호로 표시
	- 평가가 오류 없이 성공하면 해당 표현식을 ==**form**== 이라고 한다
- 예시
```clojure
0
(+ 3 4)
(list 42)
(quote (a b c)
```
- 클로저의 핵심
	- "매우 적은 수의 내장 폼(스페셜 폼)"
	- 자바의 키워드와 다른 의미의 키워드를 가지고 있음
	- 내장된 구문과 구별되지 않는 구조체 생성
- 클로저의 form과 자바의 개념적 차이
	- 자바 - 명령형 언어
		- 메모리와 상태를 메모리 위치(==박스==)로 다룬다
		- 박수는 시간에 따라 내용이 변경될 수 있다 => **"기본적으로 가변"**
		- 상태가 객체들로 이루어져 있다
	- 클로저
		- 중요한 개념은 **"값"**
		- 값은 숫자, 문자열, 벡터, 맵, 집합 등 여러가지 형태
		- 값을 생성하면 **불변이다**
		- 바인딩(==binding==) - 이름과 값 사이의 연관성을 생성
```clojure
(def <name> <value>)
```
### 10.1.1 클로저의 Hello World
- 클로저로 =="Hello World"== 작성하기
```clojure
user => (def hello (fn [] "Hello World"))
#'user/hello

user=> (hello)
"Hello World"
user=>
```
	- 식별자(identifier) hello를 값에 바인딩
	- def(심볼)는 항상 식별자를 값에 바인딩 
	- def는 바인딩(및 심볼의 이름)을 나타내는 var라는 객체를 생성
	- 실제 바인딩한 값
		- (fn [] "Hello world")
	- 바인딩 후 (hello)을 통해 실행
### 10.1.2 REPL 시작하기
- REPL
	- 사용하면 클로저 코드를 입력하고 클로저 함수를 실행 가능
	- 대화형 환경
	- 이전 평가의 결과가 남아있음
	- [[탐색적 프로그래밍]](exploratory programming)
- REPL의 기능
```clojure
user=> (hello)
"Hello world"

user=> (defn hello [] "Goodnight Moon")
#'user/hello

user=> (hello)
"Goodnight Moon"
```
	- hello에 대한 원래의 바인딩이 변경되기 전까지 유지 => 핵심적인 기능
		- 어떤 심볼이 어떤 값에 바인딩됐는지에 대한 상태는 여전히 존재
		- 이 상태는 사용자가 입력하는 각 줄들 사이에 지속
		- 클로저는 저장 위치(메모리 박스)의 내용을 시간이 지남에 따라 변경하는 것이 아닌, 심볼을 다른 시점에 다른 불변의 값에 바인딩 
		- 변수가 프로그램 수명 동안 다른 값을 가리킬 수 있다는 의미
- 시간에 따른 클로저 바인딩 변경
![[Drawing 2024-11-07 09.55.04.excalidraw]]
	- 재바인딩 - 다른 시점에 다른 박스를 가리키는 것
	- 가변 상태 - 박스의 내용물이 변경되는 것
- 함수 정의 매크로(==macro==)
	- 예시 - ==(defn)== 
	- 리스프 유사 언어의 핵심 개념
	- 내장된 구조체와 일반 코드의 차이를 최소화해야 한다는 아이디어
### 10.1.3 자주하는 실수
- 값에 ==(def)==로 바인딩하는 경우
```clojure
user=> (def hello "Goodnight Moon")
#'user/hello

user=> (hello)
Execution error (ClassCastException) at user/eval137 (REPL:1).
class java.lang.String cannot be cast to class clojure.lang.IFn
(java.lang.String si in module java.base of loader 'bootstrap';
clojure.lang.IFn is in unnamed module of loader 'app')
```
	- 클로저 var가 IFn 인터페이스를 구현한 값에 바인딩되지 않으면 런타임에 ClassCastException 발생
	- 값이 IFn을 구현하고 있지만 폼이 잘못된 인수 개수로 인해 이를 호출하려고 하면 IllegalArgumentException이 발생
- 정상적인 코드
```clojure
user=> (defn hello [] (println "Dydh da na Nor")) ;
#'user/hello

user=> (hello)
Dydh da na Nor
nil
```
### 10.1.4 괄호에 익숙해지기
- 인수가 있는 함수를 원할 경우
	- 기존 표현식 - myFunction(someObj)
	- 클로저 - myFunction someObj => 폴란드 표기법
		- AST(추상 구문 트리)==abstract syntax tree== 와 연관성이 있다

## 10.2 클로저 찾아보기: 구문 및 의미론
### 10.2.1 스페셜 폼 부트캠프
- 클로저의 일부 기본적인 스페셜 폼

| 스페셜 폼                             | 의미                                                                                         |
| --------------------------------- | ------------------------------------------------------------------------------------------ |
| (def \<symbol> <value?>)          | 주어진 값(선택적)을 심볼에 바인딩하고, 필요한 경우 해당 심볼에 대응하는 변수를 생성한다                                         |
| (fn \<name>? \[\<arg>*] \<expr>*) | 이 폼은 지정된 인수를 취해서 이를 표현식에 적응해 함숫값을 반환한다. 이것은 일반적으로 ==(def)==와 결합해 ==(defn)==과 같은 형태로 사용된다   |
| (if \<test> \<then> \<else>?)     | 만약 테스트가 논리적으로 참으로 평가된다면 ==then==을 평가하고 반환한다.그렇지 않다면 ==else==를 평가하고 반환한다(==else==가 제공되는 경우) |
| (do \<expr>*)                     | 표현식들을 왼쪽에서 오른쪽으로 순서대로 평가하고, 마지막 표현식의 값을 반환한다                                               |
| (let \[\<binding>*] \expr>*)      | 별침을 로컬 명칭으로 사용하고 암묵적으로 스코프를 정의한다. =='let'== 블록 내의 모든 표현식에서 해당 별칭을 사용할 수 있게 한다              |
| (quote \<form>)                   | 아무것도 평가하지 않고 있는 그대로 폼을 반환한다. 단일 폼을 취하고 다른 모든 인수를 무시한다                                      |
| (var \<symbol>)                   | 심볼에 해당하는 ==var==를 반환한다(값이 아닌 클로저 JVM 객체를 반환한다)                                             |
- ==var== 값, 값이 (임시로) 바인딩죄는 심볼 사이의 차이점
```clojure
user=> (def hi "Hello")
#'user/hi

// REPL에서 심볼을 그대로 사용하면 현재 해당 심볼에 바인딩된 값을 평가해서 반환
user=> hi
"Hello"

// (def) 폼에서 새로운 심볼에 값 바인딩
user=> (def bye hi)
#'user/bye

// 심볼 bye는 hi에 바인딩된 값으로 바인딩
user=> bye
"Hello"

user=> (deref bye)
"Hello"
```
	- var가 가진 값을 얻으려면 (deref) 폼을 사용
### 10.2.2 리스트, 벡터, 맵, 집합
- 클로저에서 리스트는 단일 연결 리스트
```clojure

// 리스트 생성 방법1
user=> '(1 2 3)
(1 2 3)

// 리스트 생성 방법2
user=> (quote (1 2 3))
(1 2 3)
```
	- 리스트 작성 방법
	- user 리스트는 불변의 세 요소를 가진 리스트, 숫자 1,2,3으로 구성
	- quote는 인수를 평가하지 않아 첫 번째 슬롯에 함숫값이 없어도 오류가 발생하지 않음
- 벡터 예시
```clojure
user=> (vector 1 2 3)
[1 2 3]

user=> (vec '(1 2 3))
[1 2 3]

user=> [1 2 3]
[1 2 3]

// (nth)는 두개의 인수
// 자바 list의 get 메서드와 유사
user=> (nth '(1 2 3) 1)
2
```
- 맵 예시
```clojure
user=> (def foo {"aaa":"111", "bbb":"2222})
#'user/foo

user=> foo
{"aaa":"111", "bbb":"2222}

user=> (foo "aaa")
"111"
```
- 키워드
	- 콜론==";"== 으로 시작하는 키
- 키워드와 맵의 유용한 점
	- 클로저에서의 키워드는 하나의 인수를 받는 함수이며, 이 인수는 반드시 맵이어야한다
	- 맵에 대해 키워드 함수를 호출하면, 맵의 키워드 함수에 해당하는 맵의 값이 반환된다
	- 키워드를 사용할 때 문법적으로 유용한 대칭성이 있는데, ==(my-map :key)== 와 ==(:key my-map)==은 둘 다 사용 가능하다
	- 키워드는 값으로 자기 자신을 반환한다
	- 키워드는 사용하기 전에 선언하거나 ==def==를 사용해 미리 정의할 필요가 있다
	- 클로저 함수는 값이기 때문에, 맵의 키로 사용할 수 있다
	- 쉼표는 사용가능하지만(필수요소X) 맵의 키-값 쌍을 구분하는 데 사용되며, 클로저에서는 쉼표를 공백으로 취급
	- 클로저 맵에서 키워드 이외의 심볼도 사용할 수 있다. 하지만 키워드 문법은 매우 유용하며, 코드 스타일로서 코드의 가독성과 일관성을 위해 사용할 필요가 있다
- Set
	- 자바의 HashSet과 다르게 중복키를 허용하지 않음
```clojure
user=> #{"a":"b" "c"}
#{"a":"b" "c"}

user=> #{"a":"b" "a"}
Syntax error reading source at (REPL:15:15).
Duplicate key : a
```

### 10.2.3 산술, 동등성, 기타 연산들
- 클로저에는 연산자가 없고, 대신 함수를 사용해야한다
```clojure
// 1번 방법
(add 3 4)

// 2번 방법
(+ 1 2 3)
```

- 동등성
```clojure
user=> (def list-int '(1 2 3 4))
#'user/list-int

user=> (def vect-int(vec list-int))
#'user/list-int

user=> (= vect-int list-int)
true

user=> (identical? vect-int list-int)
false
```
	- (=) 폼은 동일한 순서로 동일한 객체들로 이뤄져 있는지 확인
	- (identical?)은 실제로 두 개의 객체가 동일한 객체인지 확인
	- 심볼 이름이 케밥 케이스 사용(예시 : list-int)
- 클로저에서의 참과 거짓
	- false
	- nil
	- true
		- nil, false외 모든 것
### 10.2.4 클로저에서 함수 다루기
- 몇 가지 간단한 클로저 함수
	- 간단한 클로저 함수 정의
```clojure
(defn const-fun1 [y] 1)

(defn ident-fun [y] y)

// list-maker-fun은 두 개의 인수
// 두번째 인수는 함수
(defn list-maker-fun [x f]
	// 인라인 익명 함수
	(map (fn [z] (let [w z]
		// 값과 값을 인수로 함수 f에 적용한 결과를 두 개의 요소로 가지는 리스트 생성
		(list w (f w))
	)) x))
```
	- const-fun1 함수는 값 하나를 받아서 항상 1을 반환 => "상수 함수"
	- ident-fun 함수는 받은 값을 그대로 반환 => "항등 함수"
	- list-maker-fun은 두 개의 인수 받기
		- 첫 번째는 작업할 값들의 벡터인 x
		- 두 번째는 함수
- 함수와 함께 작업하기
```clojure
user=> (list-maker-fun ["a"] const-fun1)
(("a" 1))

user=> (list-maker-fun ["a" "b"] const-fun1)
(("a" 1)) (("b" 1))

user=> (list-maker-fun [2 1 3] ident-fun)
((2 2) (1 1) (3 3))

// 두 번째 인수가 함수가 아니라서 에러 발생
user=> (list-maker-fun [2 1 3] "a")
java.lang.ClassCastException: java.lang.String cannot be cast to 
	clojure.lang.IFn
	
```
- 슈바르츠 변환(==Schwarizian transform==)
```clojure
user=> (defn schwarz [x key-fn]
	(map (fn [y] (nth yy 0))
		(sort-by (fn [t] (nth t 1))
			(map (fn [z] (let [w z]))
				(list w (key-fn w))
			)) x)))
#'user/schwartz

user=>(schwartz [2 3 1 5 4] ident-fun)
(1 2 3 4 5)
user=> (apply schwartz [[2 3 1 5 4] ident-fun])
(1 2 3 4 5)
```
![[Pasted image 20241107142736.png]]
- ==(sort-by)==
	- 필요 인수 - (정렬을 수행할 함수, 정렬될 백터)
- ==(apply)==
	- 필요 인수 - (호출할 함수, 그 함수에 전달할 인수들이 담긴 벡터)
### 10.2.5 클로저에서 루프
- ==loop-recur== (클로저에서 루프)
```clojure
(defn like-for [counter]
	(loop [ctr counter] ;<- loop의 진입점
		(println ctr)
		(if (< ctr 10)
			(recur (inc ctr)) ; <- 뒤로 점프할 recur
			ctr
	)))
```
	- 인수들의 벡터를 가져감
	- 사실상 (let)이 하느 ㄴ것과 동일한 별칭
	- 실행이 (recur) 폼에 도달하면 (recur)은 새로 지정된 값과 함께 다시 (loop) 폼으로 분기

### 10.2.6 리더 매크로와 디스패치
- 리더 매크로(reader macro)
	- 클로저 파서가 자체적으로 사용하기 위해 예약된 문자들
- 리더 매크로 표

| 캐릭터 | 명칭    | 설명                                                                                             |
| --- | ----- | ---------------------------------------------------------------------------------------------- |
| '   | Quote | (quote)로 확장되며 평가되지 않은 폼을 반환한다                                                                  |
| ;   | 주석    | 한 줄 주석을 표시하며, 자바의 ==//==와 유사하다                                                                 |
| \   | 문자    | 리터럴 문자를 생성한다. 예를 들어 새 줄 문자를 표현하는 ==\n==이 있다                                                    |
| @   | Deref | ==var== 객체를 받아 해당 객체의 값을 반환하는 ==(deref)==로 확장된다(==var==) 폼의 반대동작. 트랜잭션 메모리 콘텍스트에서 추가적인 의미를 가진다 |
| ^   | 메타데이터 | 객체에 메타데이터 맵을 첨부한다.                                                                             |
| ``  | 문법 인용 | 주로 매크로 정의에서 사용되는 quote 폼이다.                                                                    |
| \#  | 디스패치  | 여러 다른 하위 형식이 있다                                                                                |
- 디스패치 리더 매크로의 하위 형식

| 디스패치 폼        | 설명                                       |
| ------------- | ---------------------------------------- |
| #'            | ==(var)==로 확장된다                          |
| #{}           | set 리터럴 생성                               |
| #()           | 익명 함수 리터럴 생성. ==(fn)==보다 더 간결한 단일 사용에 유용 |
| \#_           | 다음 폼을 건너뛴다. 여러 줄의 주석을 작석                 |
| #"\<pattern>" | 정규식 리터럴을 생성한다                            |

	- 디스패치 폼의 특징
		- (#') 폼은 (def) 이후의 REPL의 출력을 설명해줌
		- (def) 폼은 새로 생성된 var 객체를 반환

## 10.3 함수형 프로그래밍과 clojure
- ==(filter)==
```clojure
user=> (defn gt4 [x] (> x 4))
#'user/gt4
user=> (filter gt4 [1 2 3 4 5 6])
(5 6)
```
- (reduce)
	- 초기 시작값을 인수로 받거나 받지 않는 폼
```clojure
user=> (reduce + 1 [2 3 4 5])
15
user=> (reduce + [1 2 3 4 5])
15
```

- 예제
```clojure
user=> (defn adder [constToAdd] #(+ constToadd %1)) ; adder : 다른 함수를 생성하는 함수
#'user/adder ; 인수 1개만 사용

user=> (def plus2 (adder 2)) 
#'user/plus2

user=> (plus2 2)
5
user=> (plus2 3)
7
```

## 10.4 클로저 시퀀스 소개
- 시퀀스(sequence)
	- 불변성
		- 문제없이 함수(및 스레드) 간에 시퀀스를 전달할 수 있어야 한다
	- 더 견고한 반복자와 유사한 추상화, 특히 다중 패스 알고리즘에 적합해야 한다
	- 지연 시퀀스가 가능해야한다
	- clojure.lang.ISeq
```java
interface ISeq {
	// 시퀀스에서 첫 번째 요소인 객체를 반환
	Object first();
	// 시퀀스에서 첫 번째 요소를 제외한 나머지를 요소를 포함하는 새로운 시퀀스로 반환
	ISeq rest();
}
```
	- first() 호출은 멱등성(idempotent)를 가진다
		- seq를 변경하지 않고 계속해서 같은 값을 반환
- 기본 시퀀스 함수

| 함수                          | 효과                                                                |
| --------------------------- | ----------------------------------------------------------------- |
| (seq \<col>)                | 주어진 컬렉션의 뷰 역할을 하는 시퀀스를 반환                                         |
| (first \<col>)              | 컬렉션의 첫 번째 요소를 반환하고, 필요한 경우 (seq)를 먼저 호출한다. 컬렉션이 nil인 경우 nil을 반환한다 |
| (rest \<cols>)              | 컬렉션에서 첫 번째 요소를 제외한 새로운 seq를 반환한다                                  |
| (seq? \<o>)                 | o가 시퀀스인 경우 true를 반환한다(ISeq를 구현하는 경우)                              |
| (cons \<elt> \<coll>)       | 컬렉션에 새 요소를 앞에 추가한 시퀀스를 반환한다                                       |
| (conj \<coll> \<elt>)       | 벡터인 경우에는 끝에, 리스트인 경우에는 맨 앞에 새 요소를 추가한 새 컬렉션을 반환한다                 |
| (every? \<pred-fn> \<coll>) | 컬렉션의 모든 항목에 대해 ==(pred-fn)==이 논리적으로 참을 반환하면, ==true==를 반환한다       |
- 시퀀스 함수 사용 예시
```clojure
user=> (rest '(1 2 3))
(2 3)

user=> (first '(1 2 3))
1

user=> (rest [1 2 3])
(2 3)

user=> (seq ())
nil

user=> (seq []
nil

user=> (cons 1 [2 3])
(1 2 3)

user=> (every? is-prime [2 3 5 7 11])
true
```

### 10.4.1 시퀀스와 가변 인수 함수
- 함수의 항수(arity)
	- 함수에 가변 개수의 인수를 가질 수 있는 능력
- 가변 인수 함수(==variable-arity function==)
	- 가변 개수의 매개변수를 받아들이는 함수
- 예시
```clojure
user=> (defn const-fun-arity)
	([] 1)
	([x] 1)
	([x & more] 1)
)
#'user/const-fun-arity1

user=> (const-fun-arity)
1

user=> (const-fun-arity1 2)
1

user=> (const-fun-arity1 2 3 4)
1
```

## 10.5 클로저와 자바 간의 상호 운용성
### 10.5.1 클로저에서 자바 호출하기
- 심볼 앞의 ==(.)== 
	- 런타임에 다음 인수에 지정된 메소드를 호출하도록 지시
- 예시
```clojure
user=> (defn lenStr [y] (.length (.toString y)))
#'user/lenStr

user=> (schwartz ["bab" "aa" "dgfwg" "droopy"] lenStr)
("aa" "bab" "dgfwg" "droopy")
```

### 10.5.2 클로저 호출의 특성
- 클로저의 함수 호출은 JVM 메서드 호출로 컴파일
- JVM은 리스프가 일반적으로 수행하는 [[꼬리 재귀| 꼬리 재귀 최적화]]를 보장하지 않는다
- 클로저에서 자바 객체 인스턴스 생성
```clojure
(import '(java.util.concurrent CountDownLatch LinkedBlockingQueue))

(def cdl (new CountDownLatch 2))

(def lbq (LinkedBlockingQueue.))
```

### 10.5.3 클로저값의 자바 타입
- 클로저값의 자바 타입

```clojure
user=> (.getClass "foo")
java.lang.String

user=> (.getClass 2.3)
java.lang.Double

user=> (.getClass [1 2 3])
clojure.lang.PersistenVector

user=> (.getClass '(1 2 3))
clojure.lang.PersistantList

user=> (.getClass (fn [] "Hello world!"))
user$eval110$fn_111
```
	- 모든 클로저 값은 객체
	- 클로저의 값이 자바의 참조 타입으로 매핑됨
### 10.5.4 클로저 프록시 사용하기
- ==(proxy)== 
	- 자바의 클래스를 확장하거나 인터페이스를 구현한 클로저 객체 생성 가능
	- 기본 형태
```clojure
(proxy \[\<superclass/interfaces>] \[\<args>] <impls of named functions>+)
```
	- 첫 번째 벡터 인수
		- 이 프록시 클래스가 구현해야 하는 인터페이스
	- 두 번째 벡터 인수
		- 슈퍼클래스 생성자에 전달돼야 하는 매개변수
		- 대개 빈 벡터
		- (proxy) 폼이 자바 인터페이스만 구현하는 경우에는 항상 빈 벡터
### 10.5.5 REPL을 이용한 탐색적 프로그래밍
### 10.5.6 자바에서 클로저 사용하기
- 자바에서 클로저 사용하기
```clojure
ISeq seq = StringSeq.create("foobar");

while( seq != null ){
	 Object first = seq.first();
	 System.out.println("Seq: "+ seq);
	 seq = seq.next();
}
```

## 10.6 매크로
- 매크로
	- 매크로 확장 시간(macro expansion time)
		- 컴파일 시간 중에 소스 코드 변형
	- 클로저는 동형성을 가진다
		- 프로그램이 데이터와 동일한 방식으로 표현된다는 의미
		- 소스 코드를 만나는 대로 컴파일 
			- 소스 코드가 로드될 때 JVM 바이트코드로 동적으로 컴파일
	- 문법 인용 - 비인용
		- 문법 인용
			- ==(macroexpand-1)== 폼
			- 더 이상의 매크로 확장이 없을 때까지 진행되지 않고 종료되는 특별한 리더 매크
		- 문법 비인용
			- 어떤 부분을 인용하지 않도록 하려면 연산자==(-)== 를 사용해 문법 인용에서 제외
	- 특수한 변수들
		- ==&form== 
			- 호출되는 표현식
		- ==&env==
			- 매크로 확장 지점에서의 지역 바인딩들로 이뤄진 맵을 나타냄
	- 매크로 사용의 일반적인 규칙
		- 함수로 목표를 달성할 수 있는 경우 매크로를 작성하지 말 것
		- 언어나 표준 라이브러리에 이미 존재하지 않는 기능, 능력, 패턴을 구현하기 위해 매크로를 작성할 것
	- 클로저는 protocol, data type, mutlimethod 제공



