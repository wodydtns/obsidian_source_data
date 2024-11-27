## 9.1 코틀린을 사용하는 이유
- 코틀린은 자바에 비해 많은 편의성 개선을 제공하면서  기존 프로그래밍 환경을 급격하게 변화시키지 않는다

### 9.1.1 설치
## 9.2 편의성과 간결성
>[!note]
>코틀린은 자바와 많은 시각적 유사성을 유지하면서도 써야 하는 코드를 굉장히 간소화한다

### 9.2.1 더 적은 코드로 시작하기
- 자바는 기본적으로 java.lang만을 import하지만, 코틀린은 아래의 패키지를 어디서나 제공
	- java.lang.*
	- kolin.*
	- kolin.annotation.*
	- kolin.collections.*
	- kolin.comparisons.*
	- kolin.io.*
	- kolin.ranges.*
	- kolin.sequences.*
	- kolin..text.*
	- kolin.jvm.*

### 9.2.2 변수
- 변수 예시
```Java
var i = 1
var s = "String"
// type hint
var i: Int = 1
var s: String = "String"
var n : Int = "error" // -> type mismatch 에러 발생

// 불변 변수
val s = "String"
s = "boom" // 컴파일 오류 - val은 재할당 할 수 없음 에러 발생
```

### 9.2.3 동등성
- 자바에서의 오류
```Java
// 어디선가 문자열을 받아옴
// 문자열 리터럴은 실제로 동일한 객체로 인터닝될 수 있으므로
// 잘못된 비교로 인해 잘못된 안전성을 제공
String s = retrieveString();
if (s = "A value"){
	// 이곳에 도달할 수 없음 
	// 동일한 값이지만, 별도의 참조를 사용하는 경우에도 도달하지 못함
	...

}
```
- 코틀린
```Kotlin
var s: String = retrieveString()
if (s == "A value"){
	...
	// 만약 변수 s의 값이 "A value"와 
	// 동일한 경우에는 코드가 실행된다
}
```

### 9.2.4 함수
>[!note]
>코틀린은 일급 함수를 지원한다


- 코틀린은 자바의 객체 지향 기능을 모두 지원하고, 때론 함수 자체만을 원하는 경우도 인식 가능(자바는 메서드만 사용 가능함, 클래스의 문맥을 벗어나서 재사용 가능한 코드 블록을 정의할 수 없음)
	- 예시
```Kotlin
// fun 키워드는 코틀린에서 함수를 정의
fun doTheThing(){
	println("Done!")
}

// return 지정한 함수1
fun doTheThing1(): Boolean {
	println("Done!")
	return true
}

// return 지정 & 인수를 받는 함수
fun doTheThing2(value: Int): Boolean {
	println("Done $value!")
	return true
}

// named argument(지명된 인수)를 사용한 함수
fun coordinates(x: Int, y: Int){
	// ...
}
coordinates(10,20)
// 인수의 이름을 지정한 호출로 변수의 순서에 영향을 받지 않는다
coordinates(y = 20, x =10)
```
- Default 값 설정 및 불필요한 오버로드 없이 사용 가능한 함수
```Kotlin
fun callOtherServices(value: Int, retry: Boolean = false){
	// ...
}
// 함수에서 retry는 기본값으로 false를 가진다
callOtherServices(10)
```
- 한 줄로 표현되는 함수
```Kotlin
// 타입 초른을 통한 반환 타입 생략
fun checkCondition(n: Int) = n == 42
```
- 코틀린의 람다 표현식
```Kotlin
val anotherCheck = { n: Int -> n > 100}
```
- invoke
	- 변수 이름이 함수 자체의 이름인 것처럼 호출하는 것
```Kotlin
// checkCondition 함수를 check 변수에 할당하고 호출해서 true를 출력
println(check(42))
// 람다를 호출해 값이 100보다 큰지 확인하고 false를 출력
println(anotherCheck.invoke(42))
```
- 함수의 타입을 표현하는 문법
```Kotlin
// callsAnother함수는 int를 인수로 받아 아무것도 반환하지 않는 함수를 인수로 받음
fun callsAnother(funky: (Int) -> Unit){
	funky(42)
}
// callsAnother를 호출하는데, 이 때 함수 타입이 일치하는 람다를 전달
// 최초에 람다의 타입을 완전히 명시한 원래의 호출 방식
callsAnother({n : Int -> println("Got $n")})

// callsAnother 함수가 필요한 타입이 int라는 것을 유추할 수 있으므로,
// n은 int 타입으로 추론된다
callsAnother({n -> println("Got $n")})

// 람다에 하나의 매개변수를 전달하는 패턴이 자주 사용되기 때문에
// 코틀린은 암묵적으로 매개변수로 특별한 지원을 제공
callsAnother({ println("Got $it")})
```
- 람다에  인수 전달
	- 함수에 전달하는 인수가 람다 하나뿐인 경우 괄호를 사용하지 않아도 된다
```Kotlin
callsAnother({println("Got $it")})
callsAnother(){ println("Got $it") }
callsAnother{println("Got $it")}
```

### 9.2.5 컬렉션
- 코틀린의 컬렉션 기능
```Kotlin
// 타입 추론을 통해 kotlin.collections, List<String>와
// kotlin.collections MutableList<String>으로 list를 생성한다
val readOnlyList = listOf("a", "b", "c")
val mutableList = mutableListOf("a", "b", "c")

// 타입 추론을 통해 kotlin.collections, Map<String, int>와
// kotlin.collections MutableList<String, int>으로 map을 생성한다
val readOnlyMap = mapOf("a" to 1, "b" to c, "c" to 3)
val mutMap = mutableMapOf("a" to 1, "b" to c, "c" to 3)

// 타입 추론을 통해 kotlin.collections, Set<String>와
// kotlin.collections MutableSet<String>으로 list를 생성한다
val readOnlySet = setOf(0,1,2)
val mutableMap = mutableSetOf(1,2,3)
```
	- 기본 함수들은 컬렉션의 읽기 전용 복사본으로 반환
	- 수정을 위한 인터페이스를 얻기 위해서는 명시적으로 변경 가능한 mutable 형태를 요청해야한다
	- 컬렉션 인터페이스의 자체 계층구조를 정의하고 있으나, 내부적으로 JDK의 구현을 재사용

- 코틀린 컬렉션 계층구조
![[Pasted image 20241106142854.png]]
- 하나의 컬렉션에서 각 요소를 다른 값으로 변환
```Kotlin
val l = listOf("a","b","c")
val result = l.map {it.toUpperCase() } // result는 "A","B","C"의 리스트를 포함
```
- collection에서 값 제거하기
```Kotlin
val l = listOf("a","b","c")
val result = l.filter{ it != "b" } // result 안에는 "a", "c"가 포함된 리스트가 있다
```
- all, any, none
```Kotlin
val l = listOf("a","b","c")
val all = l.all {it.length == 1} // all == true
val any = l.any {it.length == 2} // all == false
val none = l.none {it == "a" } // none == false
```
- associateWith, associateBy 
```Kotlin
val l = listOf("!", "-", "--", "---")

// resultWith는 mapOf("!" to 1, "-" to 1, "--" to 2, "---" to 3)
val resultWith = l.associateWith {it.length}
// resultBy는 mapOf(1 to "-", 2 to "--", 3 to "---")
val resultBy = l.associateBy {it.length}
```

### 9.2.6 자유롭게 표현하기
- 코틀린에서  ==if==문은 실행 흐름 제어 & 값을 반환하는 표현식으로도 사용된다
```Kotlin
// if문 1
val iffy = if(checkCondition()){
	// 조건이 true라면 "sure"가 iffy에 할당
	"sure"
}else{
	// 조건이 false라면 "nope"가 iffy에 할당
	"nope"
}

// if문 2
val myTurn = if (condition) "sure" else "nope"
```
- 삼항연산자는 코틀린에서 **제거**
- 코틀린의 switch 문 - when
``` Kotlin
val w = when(x){
	1 -> "one" // x가 1이면 "one"
	2 -> "two" // x가 2이면 "two"
	else -> "lots" // else문

}

// 예제 2 - 컬렉션에 값이 있는지 확인하는 방법
val valid = listOf(1,2,3)
val invalid = listOf(4,5,6)

val w = when(x){
	in valid -> "valid"
	in invalid -> "invalid"
	else -> "unknown"
}

// 예제 3 - 숫자 범위에 대한 값 확인 방법
val w = when(x){
	in 1..3 -> "valid"
	in 4..6 -> "invalid"
	else -> "unknown"
}

// 예제 4 - 유형이 요구한 것과 일치 시의 값 확인 방법
fun theBest() = 1
fun okValuese() = listOf(2,3)

val message = when (incoming){
	theBest() -> "best!"
	in okValue() -> "ok!"
	else -> "nope"
}
 // 예제 5 - try-catch문을 사용한 표현식 대체 
 val message = try {
	 dangerousCall()
	 "fine"
 }catch (e: Exception){
	 "oops"
 }
```

## 9.3 클래스와 객체에 대한 다른 시각
- 코틀린 사용법
```Kotlin
// 인스턴스 생성 방법
val person = Person()

// 필드 추가 방법
// 자동으로 getter, setter 제공
import java.time.LocalDate

class Person {
	val birthdate = LocalDate.of(1996,1,23)
	var name = "Name Here"
}

// 필드 접근 제한 설정하기
class Person {
	private val birthdate = LocalDate.of(1996,1,23)
	var name = "Name Here"

}
```
- 코틀린의 접근 제한
	- private
		- 해당 클래스 내부에서만 볼 수 있다
	- protected
		- 해당 클래스와 하위 클래스에서 볼 수 있다
	- internal
		- 함께 컴파일되는 코드에서 볼 수 있다
	- public
		- 누구나 볼 수 있다
	- 기본값
		- 가시성 수준을 public을 설정
- 코틀린의 위임자
```Kotlin
import kotlin.properties.Delegates

class Person {
	var name: String by Delegates.observable("Name Here"){
		prop,old,new -> println("Name changed from $old to $new")
	}
}
```
	- Delegates.observable을 호출할 때 전닭하는 값은 속성의 초기 값으로 처리
	- Delegates.observable에 전달하는 람다는 속성의 백업값을 변경한 후 호출
- 코틀린의 생성자들
```Kotlin
// 기본 생성자
class Person (
	val birthdate: Localdate,
	var name: String){
}

val person = Person(LocalDate.of(1996,1,23), "Somebody")

// 접근 제한자 애너테이션을 생성자에 적용해야하는 경우
// constructor 키워드 사용
class Person private constructor(
	val birthdate: LocalDate,
	var name: String){
}
// 객체 생성 중 다른 로직 실행 방법
class Person(
	val birthdate: LocalDate,
	var name:String){
	init {
		//init 블록은 속성들을 생성자로부터 할당한 후에 실행
		// 코드에서 속성에 접근 가능
		if(birthdate,year < 2000){
			println("So last century")
		}
	}	
}
// 추가적인 생성자가 필요한 경우 - 보조 생성자 사용
class Person(
	val birthdate: LocalDate,
	var name:String){

	constructor(name: String)
		// 클래스가 기본 생성자를 가지고 있을 때
		// 보조 생성자들은 this로 해당 기본 생성자를 호출해야한다
		// 보조 생성자는 기본 생성자를 직접 또는 다른 보조 생성자를 통해 호출해야한다
		:this(LocalDAte.of(0,1,1), name)
}
// 생성자 + 함수 예시
class Person(
	val birthdate: LocalDate,
	var name:String){

	fun isBirthday(): Boolean {
		return today() == birthdate
	}

	private fun today(): LocalDate {
		return LocalDateTime.now().toLocalDate();
	}
}
```
- 상속
```Kotlin
// extends 키워드 없음
// 타입 선언과 동일한 ":" 구문을 사용해 상속 표현
class Child(birthdate: LocalDate, name: String)
	: Person(birthdate,name){
}
```
- 클래스를 상속 가능한 클래스로 만드는 방법
``` Kotlin
// 기본적으로 코틀린은 상속이 불가능 => "닫힌 메서드 원칙"
// open 키워드를 통한 상속 가능화
open class Person (
	val birthdate: LocalDate,
	val name: String){

	// isBirthday가 서브클래스에서 오버라이드 되도록 선언
	open fun isBirthday():Boolean {
		return today() == birthdate
	}

	private fun today(): LocalDate{
		return LocalDateTime.now().toLocalDate()
	}
}
class Child(birthdate: LocalDate, name:String)
	:Person(birthdate, name){

	// Child 클래스는 메서드를 오버라이드는 것을 명시적으로 표시해야함
	// Child 클래스는 super를 사용해 isBirthday의 부모 구현을 호출
	override fun isBirthday(): Boolean {
		val itsToday = super.isBirthday()
		if (itsToday){
			println("YIPPY"!)
		}
		return itsToday
	}	
}
```
### 9.3.1 데이터 클래스
- 데이터 클래스
	- 단순히 데이터를 전달하기 위한 컨테이너
	- 자동을 동등성 함수(==equals, hashCode==)를 생성함
	- 적어도 하나 의상의 ==val== 또는 ==var==을 가져야함
	- ==open==으로 선언할 수 없음
		- 컴파일 시점에 타입에 대한 자식 클래스가 존재할 수 있다면 코틀린이 동등성 함수를 가질 수 없음
```Kotlin
class PlainPoint(val x: Int, val y: Int)

val pl1 = PlainPoint(1,1)
val pl2 = PlainPoint(1,1)
// 기본 equals는 참조의 동등성을 비교하므로 false 출력
println(p11 == pl2)

data class dataPoint(val x: Intm val y: Int)

val pd1 = DataPoint(1,1)
val pd2 = DataPoint(1,1)
println(pd1 == pd2) // 코틀린의 dataPoint로 true 출력
```
- 코틀린은 ==static== 대신 ==companion object==를 통해 비슷한 기능 제공
	- 클래스 내부에 존재하는 싱글톤 객체를 선언
	- 코틀린에서 object 선언은 일반적인 속성과 함수를 가진 전체 객체
	- 사용 예시 - 팩토리 메서드 방식
```Kotlin
class ShyObject private constructor(val name: String){

	companion object {
		fun create(name: String):ShyObject {
			return ShyObject(name)
		}
	}
}
ShyObject.create("The Thing")
```

## 9.4 안전성
### 9.4.1 Null 안전성
- Null 안전성
	- 자바에서 null에 대한 접근 방식
		- Optional 타입을 사용해 항상 구체적인 객체를 가지면서 ==null 대신 '없음'==값을 표시
		- @NotNull, @Nullable 애너테이션을 통한 null 처리
	- 코틀린에서 null 처리 방법
```Kotlin
// 이런 타입에 null을 할당하려고 하면 컴파일 실패
val i: Int = null
val s: String = null

// 코틀린 변수에 null을 허용하려면 "?" 접미사를 붙여야함
val i: Int? = null
val s: String? = null

// 컴파일 거부 예시
val s: String? = null
// error: only safe(?.) or non-null asserted(!!.) calls are allowed on a 
// nullable receiver of type String?
println(s.length)

// null 을 허용하는 방법1 - 안전 연산자
val s: String? = null
println(s?.length)

// null 을 허용하는 방법2 - "!!" 연산자
val s: String? = null
println(s!!.length)

// ?. 와 !! 인식을 확인하면 ?. 와 !!를 피하도록 허용
val s: String? = null

// 스마트 캐스팅
if (s != null){
	println(s.length)
}
```

### 9.4.2 스마트 캐스팅
- 스마트 캐스팅
	- is연산자를 사용 후 명시적으로 캐스트하지 않더라도 자동으로 캐스트해주는 것
	- 예시
```Kotlin
val s: Any = "Hello"
// is는 s가 String 인스턴스를 포함하는지 확인
if (s is String){
	// 컴파일러가 s는 String이라고 확신해 s를 String으로 사용함
	println(s.toUpperCase())
}
```
- 스마트 캐스팅의 제약
	- 클래스의 ==var== 프로퍼티에서 작동하지 않음
		- 스마트 캐스팅 검사가 수행된 후 다음 블록이 실행되기 전 프로퍼티가 호환 가능한 다른 하위 유형으로 동시에 변경되는 것을 방지 
## 9.5 동시성
- 코루틴
	- 가벼운 경량 스레드
	- 운영체제 수준이 아닌 런타임 내에서 구현되고 예약돼 asset 소모가 훨씬 적음
	- 코틀린에서 코루틴은 지원하는 부분은 직접 언어에 내장(==suspend 함수==)
	- 실용적으로 활용하기 위해 추가적인 라이브러리 필요(==kotlin-coroutine-core==)
	- 코루틴은 항상 어떤 scope에서 시작
		- scope는 코루틴을 어떻게 예약하고 실행할지를 제어
- GlobalScope
	- 애플리케이션의 전체 수명 동안 유지되는 스코프
```Kotlin
import kotlinx.coroutines.GlobalScope
import kotlinx.coroutines.launch

fun main(){
	GlobalScope.launch {
		println("Inside!")
	}
	println("Outside!")

}
// 
```
	- Outside 출력됨 => 이벤트 시퀸스를 생각해보면 된다
	- main 함수 실행 -> GlobalScope로 비동기적으로 코루틴 시작 -> Outside 메시지 출력 후 main 종료 -> 코루틴이 실행되는 것과 상관없이 종료
	- 코루틴이 실행되는 시간을 주면 main이 종료되기 전 코루틴이 실행가능("Inside" 호출)
- ==launch , cancel== 함수
```Kotlin
import kotlinx.coroutines.GlobalScope
import kotlinx.coroutines.delay
import kotlinx.coroutines.launch

fun main() {
	// launch에 의해 반환된 코루틴 객체를 얻는다
	val co = GlobalScope.launch {
		// 코루틴 내부에서 delay를 호출해 일정 시간 대기
		delay(1000)
		println("Inside!")
	}
	// 코루틴을 취소한다
	co.cancel()
	println("Outside")
	// 여기서 기다려도 코루틴 출력은 보이지 않는다
	Thread.sleep(2000)

}
```
	- delay 함수
		- delay 함수 선언에는 특별한 제어자(modifier)인 suspend가 있다
		- 코틀린은 suspend 함수를 코루틴 실행에서 다른 작업으로 전환하거나 취소를 찾는 것처럼 안전하게 처리해야 하는 지점이라는 것을 알고 있다 => "협력적인 멀티태스킹(cooperative multitasking)"
- 부모 코루틴과 자식 코루틴 관리
```Kotlin
import kotlinx.coroutines.GlobalScope
import kotlinx.coroutines.coroutineScope
import kotlinx.coroutines.delay
import kotlinx.coroutines.launch

fun main() {
	// 부모 코루틴 시작
	val co = GlobalScope.launch {
		// 두 개의 자식 코루틴 시작
		// coroutineScope는 해당 코루틴을 둘러싸는 스코프와 연결
		// 이 경우 전역 코루틴에 연결
		coroutineScope {
			delay(1000)
			println("first")
		}
		coroutineScope {
			delay(1000)
			println("second")
		}
	}
	// 부모 코루틴을 취소
	co.cancel()
}
```
	- 부모 코루틴 취소 시 자식 코루틴도 자동으로 취소

## 9.6 자바와의 상호 운용성
- 코틀린 컴파일러(==kotlinc==)는 자바 ==javac==와 마찬가지로 클래스 파일 생성![[Pasted image 20241107083828.png]]
- 자바와의 차이1
	- 코틀린의 클래스 밖에 있는 최상위 함수가 있음
		- ==Kt== 접미사가 붙은 클래스 생성
```Kotlin
//Main.kt
package com.wellgrounded.kotlin

fun shout(){
	println("No classes in sight"!)
}
```
	- Mainkt.class가 생성되고, 해당 함수 (shout())가 들어 있음
- 자바와의 차이2
	- 내장된 속성 처리
	- ==val== 와 ==var== 를 사용함으로써 반복적인 getter와 setter을 부담 없이 속성으로 간편하게 다룸
```Kotlin
// Person.kt
class Person(var name:String)

// App.java
public class App {
	public static void main(String[] args){
		Person p = new Person("Some Body");
		System.out.println(p.getName());

		p.setName("SomeBody");
		System.out.println(p.getName());
	}
}
```
- 자바와의 차이3
	- Named arguments
- 자바와의 차이4
	- 기본값 설정
		- 자바는 함수에 모든 인수를 명시적으로 전달해야함
		- 코틀린은 @JvmOverloads 애너테이션을 통해 함수의 필요한 변형을 명시적으로 생성하도록 지시 가능
```Kotlin
// Person.kt
class Person(var name:String){
	// 이전과 동일한 기본값을 제공하도록 하는 애너테이션
	@JvmOverloads
	fun greet(words: String = "Hi there"){
		println(words)
	}
}

public class App {
	public static void main(String[] args){
		Person p = new Person("SomeBody")
		p.greet();
		p.greet("Howdy")
	}

}
```
