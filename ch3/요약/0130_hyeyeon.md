# 3 함수 정의와 호출

> 컬렉션, 문자열, 정규식을 다루기 위한 함수
> 

> 이름 붙인 인자, 디폴트 파라미터 값, 중위 호출 문법 사용
> 

> 확장 함수와 확장 프로퍼티를 사용해 자바 라이브러리 적용
> 

> 최상위 및 로컬 함수와 프로퍼티를 사용해 코드 구조화
> 

<br>

## 3.1 코틀린에서 컬렉션 만들기

```kotlin
val list = arrayListOf(1, 7, 53)
// to는 일반 함수, 언어가 제공하는 특별한 키워드가 아님
val map = hashMapOf(1 to "one", 7 to "seven", 53 to "fifty_three")
```

**코틀린이 자체 컬렉션을 제공하지 않는 이유?**

- 표준 자바 컬렉션을 활용하면 자바 코드와 상호작용하기 훨씬 더 쉬움

**코틀린 컬렉션**

- 코틀린 컬렉션은 자바 컬렉션과 똑같은 클래스
- 하지만 코틀린에서는 자바보다 더 많은 기능을 쓸 수 있음

```kotlin
val strings = listOf("first", "second", "fourteenth")
println(strings.last()) // 리스트의 마지막 원소를 가져옴
```

```kotlin
val numbers = setOf(1, 14, 2)
println(numbers.max()) // 수로 이뤄진 컬렉션에서 촤댓값을 가져옴
```

<br>

## 3.2 함수를 호출하기 쉽게 만들기

```kotlin
fun <T> joinToString(
    collection: Collection<T>,
    separator: String,
    prefix: String,
    postfix: String
) : String {
    val result = StringBuilder(prefix)
    for ((index, element) in collection.withIndex()) {
        if (index > 0) result.append(separator)
        result.append(element)
    }
    result.append(postfix)
    return result.toString()
}
```

- 이 함수는 `Generic`함
- 즉, 어떤 타입의 값을 원소로 하는 컬렉션이든 처리 가능

```kotlin
val list = listOf(1, 2, 3)
println(joinToString(list, "; ", "(", ")"))

➡️ (1; 2; 3)
```

### 3.2.1 이름 붙인 인자

```kotlin
joinToString(collection, " ", " ", ".")
```

- 코틀린은 함수에 전달하는 인자 중 일부(또는 전부)의 이름을 명시할 수 있음
- 호출 시 인자 중 어느 하나라도 이름을 명시하고 나면 혼동을 막기 위해 그 뒤에 오는 모든 인자는 이름을 꼭 명시해야 함

### 3.2.2 디폴트 파라미터 값

- 자바에서는 일부 클래스에서 overloading한 메서드가 너무 많아진다는 문제가 있음
- 자바에서는 인자 중 일부가 생략된 오버로드 함수를 호출할 때 어떤 함수가 불릴지 모호한 경우가 생김
- 코틀린에서는 함수 선언에서 `파라미터의 디폴트 값을 지정할 수 있음` → 오버로드 중 상당수를 피할 수 있음

```kotlin
fun <T> joinToString(
    collection: Collection<T>, // 디폴트 값이 지정된 파라미터들
    separator: String = ", ",
    prefix: String = "",
    postfix: String = ""
) : String
```

- 파라미터에 디폴트 값을 지정했기 때문에 일부 생략 가능

```kotlin
joinToString(list, ", ", "", "")
joinToString(list)
joinToString(list, "; ")
```

- 이름 붙인 인자를 사용하는 경우
    - 인자 목록의 중간에 있는 인자 생략 가능
    - 정하고 싶은 인자를 이름을 붙여 순서와 관계없이 지정 가능

```kotlin
joinToString(list, postfix = ";", prefix = "# ")

➡️ # 1, 2, 3;
```

- 함수의 디폴트 파라미터 값은 함수를 호출하는 쪽이 아니라 함수 선언 쪽에서 지정됨
- 어떤 클래스 안에 정의된 함수의 디폴트 값을 바꾸고 그 클래스가 포함된 파일을 재컴파일하면 그 함수를 호출하는 코드 중에 값을 지정하지 않은 모든 이자는 자동으로 바뀐 디폴트 값을 적용 받음


> 💡 자바에는 디폴트 파라미터 값이라는 개념이 없음
> 코틀린 함수를 자바에서 호출하는 경우, 그 코틀린 함수가 디폴트 파라미터 값을 제공하더라고 모든 인자를 명시해야 함

### 3.2.3 정적인 유틸리티 클래스 없애기: 최상위 함수와 프로퍼티

- **자바**
    - 모든 코드를 클래스의 메서드로 작성해야 함
    - 이 결과 다양한 정적 메서드를 모아두는 역할만 담당하며, 특별한 상태나 인스턴스 메서드는 없는 클래스가 생겨남
- **코틀린**
    - 코틀린에서는 이런 무의미한 클래스가 필요 없음
    - 대신 함수가 정의된 패키지를 임포트해야함

```kotlin
package strings

fun joinToString(...): String { ... }
```

- 코틀린 컴파일러가 생성하는 클래스의 이름은 최상위 함수가 들어있던 코틀린 소스 파일의 이름과 대응함

**최상위 프로퍼티**

- 함수와 마찬가지로 프로퍼티도 파일의 최상위 수준에 놓을 수 있음
- 이런 프로퍼티의 값은 정적 필드에 저장됨

```kotlin
var opCount = 0

fun performOperation() {
    opCount++
    // ...
}

fun reportOperationCount() {
		println("Operation performed $opCount times") 
}
```

- 최상위 프로퍼티를 활용해 코드에 상수를 추가할 수 있음

```kotlin
// public static final 필드로 컴파일됨
const val UNIX_LINE_SEPARATOR = "\n"
```

<br>

## 3.3 메서드를 다른 클래스에 추가: 확장 함수와 확장 프로퍼티

**확장함수**

- 어떤 클래스의 멤버 메서드인 것처럼 호출할 수 있지만 그 클래스의 밖에 선언된 함수

```kotlin
package strings

fun String.lastChar(): Char = this.get(this.length - 1)
```

- **String**을 `수신 객체 타입(receiver type)`이라 부르며, 확장 함수가 호출 되는 대상이 되는 값(객체) **this**를 `수신 객체(receiver object)`라고 부름
- 수신 객체 타입은 확장이 정의될 클래스의 타입이며, 수신 객체는 그 클래스에 속한 인스턴스 객체임

```kotlin
package strings

fun String.lastChar(): Char = get(length - 1)
```

- 수신 객체 멤버에 this 없이 접근 가능

```kotlin
println("Kotlin".lastChar())

➡️ n
```

### 3.3.1 임포트와 확장 함수

> 💡 코틀린 문법상 확장 함수는 반드시 짧은 이름을 써야 함

임포트할 때 이름을 바꾸는 것이 확장 함수 이름 충돌을 해결할 수 있는 유일한 방법

- 확장 함수도 `import` 해야 사용 가능
    - 임포트하지 않고 확장 함수를 정의하자마자 어디서든 그 함수를 쓸 수 있다면 한 클래스에 같은 이름의 확장 함수가 둘 이상 있어서 이름이 충돌하는 경우가 생길 수 있어 임포트 해야 함
- 코틀린에서는 클래스를 임포트할 때와 동일한 구문을 사용해 개별 함수를 임포트 할 수 있음

```kotlin
import strings.lastChar
val c = "Kotlin".lastChar()

import strings.*
val c = "Kotlin".lastChar()
```

- as 키워드를 사용하면 임포트한 클래스나 함수를 다른 이름으로 부를 수 있음

```kotlin
import strings.lastChar as last
val c = "Kotlin".last()
```

### 3.3.2 자바에서 확장 함수 호출

- 내부적으로 확장 함수는 수신 객체를 첫 번째 인자로 받는 정적 메서드임
- 정적 메서드를 호출하면서 첫 번째 인자로 수신 객체를 넘기기만 하면 됨

```java
char c = StringUtilKt.lastChar("Java");
```

### 3.3.3 확장 함수로 유틸리티 함수 정의

```kotlin
fun <T> Collection<T>.joinToString ()		// Collection<T>에 대한 확장 함수 선언
    separator: String = ", ",
    prefix: String = "",
    postfix: String = ""					
): String {
    val result = StringBuilder(prefix)
    for((index, element) in this.withIndex()) {		// this는 수신 객체(T 타입으로 이루어진 컬렉션)
        if(index > 0) result.append(separator)
        result.append(element)
    }
    result.append(postfix)
    return result.toString()
}
```

- 확장 함수는 단지 정적 메서드 호출에 대한 문법적인 편의일 뿐

- 클래스가 아닌 더 구체적인 타입을 수신 객체 타입으로 지정할 수 있음

```kotlin
fun Collection<String>.join(
    separator: String = ", ",
    prefix: String = "",
    postfix: String = ""
) = joinToString(separator, prefix, postfix)

println(listOf("one", "two", "eight").join(" "))
```

- 확장 함수가 정적 메서드와 같은 특징을 가지므로, 확장 함수를 하위 클래스에서 오버라이드할 수 없음

### 3.3.4 확장 함수는 오버라이드할 수 없다

> 💡 코틀린은 호출될 확장 함수를 정적으로 결정하기 때문에 확장 함수를 오버라이드할 수 없음

```kotlin
open class View {
    open fun click() = println("View Clicked")
}

class Button: View() { // Button은 View를 확장함
    override fun click() = println("Button Clicked")
}
```

- 확장 함수는 클래스의 일부가 아님
- 확장 함수는 클래스 밖에 선언됨
- 확장 함수는 수신 객체로 지정한 변수의 `정적 타입`에 의해 어떤 확장 함수가 호출될 지 결정됨
    - 동적인 타입에 의해 결정되지 않음

```kotlin
fun View.showOff() = println("I'm a view!")
fun Button.showOff() = println("I'm a button!")

val view: View = Button()
view.showOff()  // 확장 함수는 정적으로 결정됨
```

### 3.3.5 확장 프로퍼티

- 확장 프로퍼티를 사용하면 기존 클래스 객체에 대한 프로퍼티 형식의 구문으로 사용할 수 있는 API를 추가할 수 있음
- 프로퍼티라는 이름으로 불리기는 하지만 상태를 저장할 방법이 없음 → 실제로 확장 프로퍼티는 아무 상태도 가질 수 없음, 대신 더 짧게 코드를 작성할 수 있어 편한 경우가 있음

```kotlin
val String.lastChar: Char
    get() = get(length - 1)
    set(value: Char) {
        this.setCharAt(length - 1, value)
    }

prinln("Kotlin".lastChar)
```

- 일반적인 프로퍼티에서 수신 객체 클래스만 추가됨
- 최소한 게터는 꼭 정의해야 함 (뒷받침하는 필드가 없어 기본 게터 구현을 제공할 수 없기 때문)

<br>

## 3.4 컬렉션 처리: 가변 길이 인자, 중위 함수 호출, 라이브러리 지원

> 💡 코틀린 언어 특성
> - vararg 키워드를 사용하면 호출 시 인자 개수가 달라질 수 있는 함수를 정의할 수 있음
> - 중위(infix) 함수 호출 구문을 사용하면 인자가 하나뿐인 메서드를 간편하게 호출할 수 있음
> - 구조 분해 선언을 사용하면 복합적인 값을 분해해서 여러 변수에 나눠 담을 수 있음

### 3.4.1 자바 컬렉션 API 확장

```kotlin
val strings = listOf("first", "second", "fourteenth")
strings.last()
```

```kotlin
val numbers = setOf(1, 14, 2)
numbers.max()
```

- `last`와 `max`는 모두 확장 함수임

```kotlin
fun <T> List<T>.last(): T {}
fun Collection<Int>.max(): Int {} 
```

### 3.4.2 가변 인자 함수: 인자의 개수가 달라질 수 있는 함수 정의

```kotlin
val list = listOf(2, 3, 5, 7, 11)
fun listOf<T> {vararg valus: T}: List<T> {...}
```

- **가변 길이 인자(varargs)**
    - 메서드를 호출할 때 원하는 개수만큼 값을 인자로 넘기면 자바 컴파일러가 배열에 그 값들을 넣어주는 기능
    - **문법**
    
    | Java | Kotlin |
    | --- | --- |
    | 타입 뒤에 …를 붙임  | 파라미터 앞에 vararg 변경자를 붙임 |
    - **문법 :** 이미 배열에 들어 있는 인자를 넘길 때
    
    | Java | Kotlin |
    | --- | --- |
    | 배열을 그냥 넘기면 됨 | 코틀린에서는 배열을 명시적으로 풀어서 배열의 각 원소가 인자로 전달되게 해야 함
    → 스프레드 연산자가 이런 작업을 해줌 |

```kotlin
fun main(args: Array<String>) {
		val list = listOf("args: ", *args) // 스프레드 연산자가 배열의 내용을 펼쳐줌
		println(list)
```

- 스프레드 연산자를 통하면 배열에 있는 값과 다른 여러 값을 함께 써서 함수를 호출할 수 있음
    
    → 자바에서는 사용할 수 없음
    

### 3.4.3 값의 쌍 다루기: 중위 호출과 구조 분해 선언

```kotlin
val map = mapOf(1 to "one", 7 to "seven", 53 to "fifty-three")
```

- `to` : 코틀린 키워드가 아니라, `중위 호출(infix call)`로 to라는 일반 메서드를 호출한 것
- 중위 호출 시 수신 객체와 유일한 메서드 인자 사이에 메서드 이름을 넣음

```kotlin
1.to("one") // "to" 메서드를 일반적인 방식으로 호출
1 to "one" // "to" 메서드를 중위 호출 방식으로 호출
```

- 인자가 하나뿐인 일반 메서드나 확장 함수에 중위 호출을 사용할 수 있음

- 함수를 중위 호출에 사용하게 허용하고 싶으면 infix 변경자를 함수 선언 앞에 추가해야함

```kotlin
infix fun Any.to(other: Any) = Pair(this, other)
```

- 위 to 함수는 Pair의 인스턴스를 반환

- Pair의 내용으로 두 변수를 즉시 초기화 할 수 있음
- 이런 기능을 `구조 분해 선언`이라고 부름

```kotlin
val (number, name) = 1 to "one"
```

```kotlin
for ((index, element) in collection.withIndex()) {
    println("$index: $element")
}
```

- 루프에서도 구조 분해 선언을 활용할 수 있음
- mapOf의 경우에는 각 인자가 키와 값으로 이뤄진 순서쌍이어랴야 함

<br>

## 3.5 문자열과 정규식 다루기

### 3.5.1 문자열 나누기

- 코틀린에서는 자바의 split 대신에 여러가지 다른 조합의 파라미터를 받는 split 확장 함수를 제공함
    - 정규식을 파라미터로 받는 함수는 String이 아닌 Regex 타입의 값을 받음
        
        → 따라서 코틀린에서는 split 함수에 전달하는 값의 타입에 따라 정규식이나 일반 테스트 중 어느 것을 문자열로 분리하는지 쉽게 알 수 있음
        

```kotlin
// 정규식을 명시적으로 만듬, 간단한 경우 꼭 정규식을 쓸 필요가 없음
println("12.345-6.A").split("\\.|-".toRegex())	

// 여러 구분 문자열을 지정
println("12.345-6.A").split(".", "-")

➡️ [12, 345, 6, A]
```

### 3.5.2 정규식과 3중 따옴표로 묶은 문자열

```kotlin
// String 확장 함수를 사용해 경로 파싱하기
fun parsePath(path: String) {
    val directory = path.substringBeforeLast("/")
    val fullName = path.substringAfterLast("/")
    
    val fileName = fullName.substringBeforeLast(".")
    val extension = fullName.substringAfterLast(".")
    
    println("Dir: $directory, name: $fileName, ext: $extension")
}

parsePath("/Users/hyeyeon/kotlin-book/chapter.adoc")
```

- 코틀린에서는 정규식을 사용하지 않고도 문자열을 쉽게 파싱할 수 있음
- 정규식은 강력하기는 하지만 나중에 알아보기 힘든 경우가 많음
- 정규식이 필요할 때는 코틀린 라이브러리를 사용하면 편함

```kotlin
// 경로 파싱에 정규식 사용하기
fun parsePath(path: String) {
    // 3중 따옴표 문자열을 사용해 정규식 씀
    // 3중 따옴표 문자열에서는 역슬래시(\)를 포함한 어떤 문자도 이스케이프할 필요가 없음
    // 슬래시와 마침표를 기준으로 경로를 세 그룹으로 분리
    // 패턴 .은 임의의 문자와 매치될 수 있음
    val regex = """(.+)/(.+)\.(.+)""".toRegex()
    val matchResult = regex.matchEntire(path)
    
    if(matchResult != null) {
        val (directory, fileName, extension) = matchResult.destructured
        println("Dir: $directory, name: $fileName, ext: $extension")
    }
}
```

### 3.5.3 여러 줄 3중 따옴표 문자열

- 3중 따옴표를 쓰면 줄 바꿈이 들어있는 프로그램 텍스트를 쉽게 문자열로 만들 수 있음

```kotlin
val kotlinLogo = """| //
                   .| //
                   .|/ \"""
```

<br>

## 3.6 코드 다듬기: 로컬 함수와 확장

- 자바 코드를 작성할 때는 `DRY(Don’t Repeat Yourself)` 원칙을 피하기 쉽지 않음

```kotlin
// 코드 중복을 보여주는 예제
class User(val id: Int, val name: String, val address: String)

fun saveUser(user: User) {
    if(user.name.isEmpty()) { // 필드 검증이 중복됨
        throw IllegalArgumentException(
            "Can't save user ${user.id}: Empty Name"
        )
    }
    
    if(user.address.isEmpty()) { // 필드 검증이 중복됨
        throw IllegalArgumentException(
            "Can't save user ${user.id}: Empty Address"		
        )
    }
    
    // user를 데이터베이스에 저장
}
```

- 코틀린에서는 함수에서 추출한 함수를 원 함수 내부에 중첩시킬 수 있음
- 위 검증 코드를 로컬 함수로 분리하면 중복을 없애는 동시에 코드 구조를 깔끔하게 유지할 수 있음

```kotlin
// 로컬 함수를 사용해 코드 중복 줄이기
class User(val id: Int, val name: String, val address: String)

fun saveUser(user: User) {
    // 한 필드를 검증하는 로컬 함수를 정의
    fun validate(user: User, value: String, fieldName: String) {
        if(value.isEmpty()) {
            throw IllegalArgumentException(
                "Can't save user ${user.id}: empty ${fieldName}"
            )
        }
    }
    
    validate(user, user.name, "Name") // 로컬 함수를 호출해서 각 필드를 검증
    validate(user, user.address, "Address")	
    // user를 데이터베이스에 저장
}
```

- 로컬 함수는 자신이 속한 바깥 함수의 모든 파라미터 변수를 사용할 수 있음

```kotlin
// 로컬 함수에서 바깥 함수의 파라미터 접근하기
class User(val id: Int, val name: String, val address: String)

fun saveUser(user: User) {
    // saveUser 함수의 user 파라미터를 중복 사용하지 않음
    fun validate(value: String, fieldName: String) {
        if(value.isEmpty()) {
            throw IllegalArgumentException(
                // 바깥 함수의 파라미터에 직접 접근 가능
                "Can't save user ${user.id}: " +
                   "empty $fieldName"
            )
        }
    }
    
    validate(user, user.name, "Name")
    validate(user, user.address, "Address")	
    // user를 데이터베이스에 저장
}
```

- 검증 로직을 User 클래스를 확장한 함수로 만들 수도 있음

```kotlin
// 검증 로직을 확장 함수로 추출하기
class User(val id: Int, val name: String, val address: String)

fun User.validateBeforeSave() {
    fun validate(value: String, fieldName: String) {
        if (value.isEmpty()) {
            throw IllegalArgumentException(
                "Can't save user $id: empty $fieldName" // User의 프로퍼티를 직접 사용 가능
            )
        }
    }
    
    validate(name, "Name")
    validate(address, "Address")
}

fun saveUser(user: User) {
    user.validateBeforeSave() // 확장 함수를 호출
    // user를 데이터베이스에 저장
}
```

- 확장 함수를 로컬 함수로 정의할 수도 있음
- 즉, User.validateBeforeSave를 saveUser 내부에 로컬 함수로 넣을 수 있음
- 하지만 중첩된 함수의 깊이가 깊어지면 코드를 읽기가 어려워짐
- **일반적으로는 한 단계만 함수를 중첩시키라고 권장**
