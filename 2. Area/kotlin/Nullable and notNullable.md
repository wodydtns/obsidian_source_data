```kotlin
fun main(){
	val s1 = "abc"
	// 컴파일 오류
	// val s2: String = null

	// Nullable
	val s3: String? = null
	val s4: String? = s1

	// 컴파일 오류
	// val s5: String = s4
	val s6 = s4

	s1 eq "abc"
	s3 eq null
	s4 eq "abc"
	s6 eq "abc"
}
```