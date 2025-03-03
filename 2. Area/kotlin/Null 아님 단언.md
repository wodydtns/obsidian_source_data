# non-null assertion
```kotlin
fun main(){
	var x: String? = "abc"
	// x가 null일 수도 있다는 사실을 무시하라. 내가 x가 null이 아니라는 점을 보증한다
	x!! eq "abc"
	x = null
	capture {
		val s: String = x!!
	} eq "NullPointerException"
}
```