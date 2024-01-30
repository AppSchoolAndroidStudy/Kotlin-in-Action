***

## [1] Kotlin 에서 컬렉션 만들기

~~~kotlin
val set = hashSetOf(1, 7, 33)			// 숫자로 이루어진 집합
val list = arrayListOf(1, 7, 33)	// 리스트
val map = hashMapOf(1 to "one", 7 to "seven", 11 to "eleven")		// 맵
~~~

~~~kotlin
println(set.javaClass)	// Class java.util.HashSet
println(list.javaClass)	// Class java.util.ArrayList
println(map.javaClass)	// Class java.util.HashMap
~~~

- Kotlin은 자신만의 컬렉션 기능을 제공하지 않는다. ( = 자바와 상호 간 호출할 때, 서로 변환 할 필요가 없다. )
- Kotlin의 컬렉션은 Java보다 더 많은 기능을 사용할 수 있다.

~~~kotlin
val strings = listOf("first", "second", "fourteenth")
println(strings.last())	// fourteenth [리스트의 마지막 원소를 가져오는 기능]
~~~

```kotlin
val numbers = setOf(1, 14, 2)
println(numbers.max())	// 14 [수로 이루어진 컬렉션의 최댓값을 구하는 기능]
```

***

## [2] 함수를 호출하기 쉽게 만들기

~~~kotlin
val list = listOf(1, 2, 3)
println(list)	// [1, 2, 3]
~~~

- Java의 컬렉션에는 Default로 toString( )이 구현되어 있다.

~~~kotlin
fun <T> joinToString(
  collection: Collection<T>, separator: String, prefix: String, postfix: String): String {
  val result = stringBuilder(prefix)
  for((index, element) in collection.withIndex()) {
    if(index > 0) result.append(seperator)	// 첫 원소 앞에는 구분자를 붙히면 안됨.
    result.append(element)
  }
  result.append(postfix)
  return result.toString()
}
~~~

- 해당 함수는 Generic하여, 어떤 값의 원소로 하는 컬렉션이든 처리하는 것이 가능하다.

~~~kotlin
val list = listOf(1, 2, 3)
println(joinToString(list, ";", "(", ")"))	// (1; 2; 3)
~~~

***

#### (1) 이름 붙인 인자

~~~kotlin
joinToString(collection, " ", " ", ".")
~~~

~~~kotlin
joinToString(collection, separator = " ", prefix = " ", postfix = ".")
~~~

- Kotlin으로 작성한 함수를 호출할 때는 함수에 전달하는 인자 중 일부(또는 전부)의 이름을 명시할 수 있다.
- 호출 시 인자 중 어느 하나라도 이름을 명히사고 나면 혼동을 막기 위해서 그 뒤에 오는 모든 인자는 이름을 명시해야 한다.

- IntelliJ IDEA는 호출 대상 함수가 파라미터의 이름을 바꾼 경우, 
  그 함수를 호출할 때 사용한 이름을 붙인 인자의 이름을 추적할 수 있다.

***

#### (2) 디폴트 파라미터 값

~~~kotlin
fun <T> joinToString(
	collection: Collection<T>,
  separator: String = ", ",
  prefix: String = "",
  postfix: String = ""
): String
~~~

- Java에서는 일부 클래스에서 Overloading한 메서드가 너무 많아진다는 문제가 발생한다.
- Kotlin에서는 함수 선언에서 파라미터의 디폴트 값을 지정할 수 있으므로 해당 문제가 덜하다.

```kotlin
joinToString(list, ",", "", "")		// 1, 2, 3
joinToString(list)								// 1, 2, 3 > separator, prefix, postfix 생략
joinToString(list, "; ")					// 1; 2; 3 > separator을 ';', 나머지는 생략
joinToString(list, postfix = ";", prefix = "# ")	// # 1, 2, 3;
```

- 함수의 디폴트 파라미터 값은 함수를 호출하는 쪽이 아니라 함수를 선언하는 쪽에서 지정된다.
- 어떤 클래스 안에 정의된 함수의 디폴트 값을 바꾸고 그 클래스가 포함된 파일을 재컴파일 하려면,
  해당 함수를 호출하는 코드 중에서 값을 지정하지 않은 모든 인자는 자동으로 바뀐 디폴트 값을 적용받는다.

> Java는 디폴트 파라미터가 존재하지 않으므로 Kotlin을 Java에서 호출하는 경우에는 모든 인자를 명시해야 한다.

***

#### (3) 정적인 유틸리티 클래스 없애기: 최상위 함수와 프로퍼티

- 객체지향 언어인 Java에서는 모든 코드를 클래스의 메서드로 작성해야 하지만, 실전에서는 어려운 경우가 존재한다.
- 중요한 객체는 하나뿐이지만, 그 연산을 객체의 인스턴스 API에 추가해서 API를 너무 크게 만들고 싶지 않은 경우가 있다.
- Kotlin에서는 대신 함수를 직접 소스파일의 최상위 수준에 위치시키고, 그 함수가 정의된 패키지를 임포트 하면 된다.

~~~kotlin
package strings

fun joinToString(...): String {...}
~~~

- Kotlin 컴파일러가 생성하는 클래스의 이름은 최상위 함수가 들어있던 코틀린 소스 파일의 이름과 대응한다.

<br/>

[최상위 프로퍼티]

~~~kotlin
var opCount = 0						// 최상위 프로퍼티 선언
fun performOperation() {
  opCount++								// 최상위 프로퍼티 값 변경
}
fun reportOperationCount() {
  println("Operation performed $opCount times")		// 최상위 프로퍼티 값 읽어옴
}
~~~

- 함수와 마찬가지로 프로퍼티 또한 최상위 수준에 놓일 수 있다.
- 이런 프로퍼티의 값은 정적 필드에 저장되며, 이를 활용해 코드에 상수를 추가할 수 있다.

~~~kotlin
const val UNIX_LINE_SEPARATOR = "\n"
~~~

- const 변경자를 추가하면 프로퍼티를 public static final 필드로 컴파일하는 것이 가능하다.

***

## [3] 메서드를 다른 클래스에 추가: 확장 함수와 확장 프로퍼티

~~~kotlin
package strings
fun string.lastChar(): Char = this.get(this.length - 1)
~~~

~~~kotlin
fun string.lastChar(): Char = get(length - 1)
~~~

~~~kotlin
println("Kotlin".lastChar())	// n		-> Kotlin이 수신 객체, 타입은 String
~~~

- 확장 함수는 어떤 클래스의 멤버 메서드인 것처럼 호출할 수 있지만 그 클래스의 밖에 선언된 함수이다.
- 확장 함수를 만들기 위해선 추가하려는 함수 이름 앞에 그 함수가 확장할 클래스의 이름을 덧붙이면 된다.
- 클래스 이름을 수신 객체 타입(String)이라 하며, 호출되는 대상이 되는 값을 수신 객체(this) 라고 한다.
- 호출할 때는 다른 일반 클래스 멤버를 호출할 때와 다르지 않다.
- 확장 함수 본문에서도 일반 메서드와 마찬가지로 this를 생략하는 것이 가능하다.

***

#### (1) 임포트와 확장 함수

~~~kotlin
import strings.lastChar as last

val c = "Kotlin".last()
~~~

- 확장 함수를 사용하기 위해서는 임포트를 해야만 한다. (*를 통해서도 가능)
- as 키워드를 사용하면 임포트한 클래스나 함수를 다른 이름으로 바꾸는 것이 가능하다.
- Kotlin 문법 상 확장 함수는 반드시 짧은 이름을 사용해야 하므로, 임포트할 때 이름을 바꾸는 것이 충돌의 해결 방법이다.

***

#### (2) Java에서 확장 함수 호출

~~~Java
char c = StringUtilKt.lastChar("Java");
~~~

- 확장 함수는 수신 객체를 첫 번째 인자로 받는 정적 메서드 이므로, 호출하더라도 실행 시점 부가 비용이 들지 않는다.
- 자바에서 확장 함수를 사용하기 위해서는 정적 메서드를 호출하면서 첫 번째 인자로 수신 객체를 넘기기만 하면 된다.

***

#### (3) 확장 함수로 유틸리티 함수 정의

~~~kotlin
fun <T> Collection<T>.joinToString ()		// Collection<T>에 대한 확장 함수 선언
  separator: String = ", ",
  prefix: String = "",
  postfix: String = ""									// 파라미터에 디폴트 값 지정
): String {
  val result = StringBuilder(prefix)
  for((index, element) in this.withIndex()){		// this는 수신 객체 -> T 타입으로 이루어진 컬렉션
  	if(index > 0) result.append(separator)
  	result.append(element)
	}
  result.append(postfix)
  return result.toString()
}
~~~

~~~kotlin
val list = listOf(1, 2, 3)
println(list.joinToString(separator = "; ", prefix = "(", postfix = ")"))
// (1; 2; 3)
~~~

```kotlin
val list = arratListOf(1, 2, 3)
println(list.joinToString(" "))
// 1 2 3
```

- 확장 함수는 정적 메서드 호출에 대한 문법적인 편의라고 생각하면 된다.
- 이는 클래스가 아닌 더 구체적인 타입을 수신 객체 타입으로 지정할 수도 있다.
- 확장 함수가 정적 메서드와 같은 특징을 가지므로, 하위 클래스에서 오버라이딩하는 것이 불가능하다.

***

#### (4) 확장 함수는 오버라이드 할 수 없다.

~~~kotlin
open class View {
  open fun click() = println("View Clicked")
}
class Button: View() {
  override fun click() = println("Button Clicked")
}
~~~

- Button이 View의 하위 타입이기 때문에 View 타입 변수를 선언해도 Button 타입 변수를 그 변수에 대입할 수 있다.
- View 타입 변수에 의해서 click과 같은 일반 메서드를 호출했는데, 
  click을 Button 클래스가 오버라이드 했다면 실제로는 Button이 오버라이드한 click이 호출된다.

~~~kotlin
val view: View = Button()
view.click()			// 'view'에 저장된 값의 실제 타입에 따라서 호출할 메서드가 결정된다.
// Button Clicked
~~~

- 확장 함수는 클래스 밖에 선언되므로 클래스의 일부가 아니다.

~~~kotlin
fun View.showOff() = println("I'm a view!")
fun Button.showOff() = println("I'm a button!")

val view: View = Button()
view.showOff()		// 확장 함수는 정적으로 결정된다.
// I'm a view
~~~

- view가 가리키는 객체의 실제 타입이 Button이지만, view 타입이 View이기 때문에 View의 확장 함수가 호출된다.

***

#### (5) 확장 프로퍼티

~~~kotlin
val string.lastChar: Char
		get() = get(length - 1)
~~~

- 확장 프로퍼티를 사용하면 기존 클래스 객체에 대한 프로퍼티 형식의 구문으로 사용할 수 있는 API를 추가할 수 있다.
- 확장 함수의 경우와 마찬 가지로 프로퍼티도 비슷하다. 단지 수신 객체 클래스가 추가되었을 뿐이다.
- 기본 게터 구현을 제공할 수 없으므로 최소한의 게터는 정의해야 한다.(뒷받침하는 필드)

~~~kotlin
var stringBuilder.lastChar: Char
		get() = get(length - 1)
		set(value: Char) {
      this.setCharAt(length - 1, value)
    }
~~~

~~~kotlin
println("Kotlin".lastChar)		// n
val sb = StringBuilder("Kotlin?")
sb.lastChar = "!"
println(sb)			// Kotiln!
~~~

***

## [4] 컬렉션 처리: 가변 길이 인자, 중위 함수 호출, 라이브러리 지원

[Kotlin 언어의 특성]

- varang 키워드를 사용하면 호출 시 인자 개수가 달라질 수 있는 함수를 정의할 수 있다.
- 중위 함수 호출 구문을 사용하면 인자가 하나뿐인 메서드를 간편하게 호출할 수 있다.
- 구조 분해 선언을 사용하면 복합적인 값을 분해해서 여러 변수에 나눠 담을 수 있다.

***

#### (1) Java 컬렉션 API 확장

~~~kotlin
val strings: List<String> = listOf("first", "second", "fourteenth")
strings.last()	// fourteeth

val numbers: Collection<Int> = setOf(1, 14, 2)
numbers.max()		// 14
~~~

~~~kotlin
fun <T> List<T>.last(): T { /* 마지막 원소를 반환하는 내용 */}
fun Collection<Int>.max(): Int { /* 컬렉션의 최댓값을 찾는 내용 */ }
~~~

- 해당 last와 max는 확장함수이다.
- last는 List의 확장함수이다.
- 컬렉션이나 다른 객체에 사용할 수 있는 메서드나 함수는 IDE의 코드 완성 기능에서 살펴볼 수 있다.

***

#### (2) 가변 인자 함수: 인자의 개수가 달라질 수 있는 함수

~~~ kotlin
val list = listOf(2, 3, 5, 7, 11)
fun listOf<T> {varang valus: T}: List<T> {...}
~~~

- varangs는 자바의 가변길이 인자로, 메서드를 호출할 때 원하는 개수 만큼 값을 인지로 넘기는 값을 넣어주는 기능이다.
- Kotlin의 경우에는 비슷하지만 문법이 다르다. 타입 뒤에 ...을 붙이는 대신 파라미터 앞에 varang 변경자를 붙인다.
- Java는 이미 들어있는 원소의 배열을 가변 길이 인자로 넘길 때 그냥 넘기면 되지만,
  Kotlin에서는 배열을 명시적으로 풀어서 배열의 각 원소가 인자로 전달되게 해야 한다.
- 위의 역할을 해주는 것이 **스프레드 연산자**이다.

~~~kotlin
fun main(args: Array<String>) {
  val list = listOf("args: ", *args)	// 스프레드 연산자가 배열의 내용을 펼쳐준다.
  println(list)
}
~~~

- 위 예제는 스프레드 연산자를 통하면 배열에 들어있는 값과 다른 여러 값을 함께 써서 함수를 호출할 수 있음을 보여준다.

***

#### (3) 값의 쌍 다루기: 중위 호출과 구조 분해 선언

~~~kotlin
val map = mapOf(1 to "one", 7 to "seven", 11 to "eleven")
~~~

~~~kotlin
infix fun Any.to(other: Any) = Pair(this, other)
~~~

~~~kotlin
fun<K, V> mapOf(varang values: Pair<K, V>): Map<K, V>
~~~

```kotlin
val(number, name) = 1 to "one"
```

~~~kotlin
for((index, element) in collection.withIndex()) {
  println("$index: $element")
}
~~~

- 해당 코드는 중위 호출이라는 방식으로 to라는 일반 메서드를 호출한 방식이다. 여기서 to는 Kotlin 키워드가 아니다.
- 중위 호출 시에는 수신 객체와 유일한 메서드 인자 사이에 메서드 이름을 넣는다.
- 인자가 하나뿐인 메서드나 확장함수에 중위 호출을 사용할 수 있다.
- 함수를 중위 호출에 사용하게 허용하고 싶으면 infix 변경자를 함수 선언 앞에 추가해야 한다.

- to 함수는 Kotlin의 표준 라이브러리 함수인 Pair의 인스턴스를 반환한다. 순서쌍을 표현하는 클래스이다.
- Pair를 통해 두 변수를 즉시 초기화 하는 해당 기능을 구조 분해 선언이라고 한다.
- 루프에서도 withIndex를 구조 분해 선언과 함께 조합하면 컬력센 원소와 인덱스의 값을 따로 변수에 담을 수 있다.
- to 함수는 확장함수이다. 이는 타입과 관계없이 임의의 순서쌍을 만드는 것이 가능하다.
- mapOf 같은 경우도 원하는 개수만큼 인자를 전달하는 것이 가능하지만, 각 인자가 키와 값의 순서쌍이어야 한다.

***

## [5] 문자열과 정규식 다루기

#### (1) 문자열 나누기

~~~kotlin
println("12.345-6.A").split("\\.|-".toRegex())	// 정규식을 명시적으로 만든다.
println("12.345-6.A").split(".", "-")		// 여러 구분 문자열을 지정해도 동일하다.
// [12, 345, 6, A]
~~~

- Kotlin에서는 Java의 split을 대신하여 여러가지 파라미터를 받는 split 확장 함수를 제공한다.
- 정규식을 파라미터로 받는 함수는 String이 아닌 Regex 타입의 값을 받으므로, 
  어느 것으로 문자열을 분리하는 지 쉽게 알 수 있다.

***

#### (2) 정규식과 3중 따옴표로 묶은 문자열

~~~kotlin
fun parsePath(path: String) {
  val directory = path.substringBeforeLast("/")
  val fullName = path.substringAfterLast("/")
  
  val fileName = fullName.substringBeforeLast(".")
  val extension = fullName.substringAfterLast(".")
  
 println("Dir: $directory, name: $fileName, ext: $extension")
}

parsePath("/Users/kyungwon/kotlin-book/chapter.md")
// Dir: /Users/kyungwon/kotlin-book, name: chapter, extension: md
~~~

~~~kotlin
fun parsePath(path: String) {
  val regex = """(.+)/(.+)\.(.+)""".toRegex()
  val matchResult = regex.matchEntire(path)
  if(matchResult != null) {
    val (directory, fileName, extension) = matchResult.destructured
    println("Dir: $directory, name: $fileName, ext: $extension")
  }
}
~~~

- 코틀린에서는 정규식을 사용하지 않고도 문자열을 파싱하는 것이 가능하지만 알아보기 힘든 경우가 많다.
- 다음과 같은 작업을 정규식을 활용하여 구현하는 것이 가능하다.
- 3중 따옴표 문자열에서는 역슬래시를 포함한 어떤 문자도 이스케이프할 필요가 없다.
- 해당 예제에서 사용한 정규식은 슬래시와 마침표를 기준으로 경로를 세 그룹으로 분리한다.
- 패턴 .은 임의의 문자와 매치될 수 있다.

***

## [6] 코드 다듬기

- DRY 원칙(Don't Repeat Yourself)을 피하기는 쉽지 않다.
- 메서드 추출 리팩토링을 통해서 긴 메서드를 부분부분 나눠서 재활용 하는 것은 가능하다.
- 해당 해결방법의 경우 더 복잡해지기에, 메서드를 별도의 내부 클래스에 넣게 된다면 불필요한 준비코드가 늘어난다.
- Kotlin에서는 추출한 함수를 원 함수에 중첩시킬 수 있다.

~~~kotlin
class User(val id: Int, val name: String, val address: String)

fun saveUser(user: User) {
  if(user.name.isEmpty()) {
    throw IllegalArgumentException(
    	"Can't save user ${user.id}: Empty Name"
    )
  }
  
  if(user.address.isEmpty()) {
    throw IllegalArgumentException(
    	"Can't save user ${user.id}: Empty Address"		// 필드 검증이 중복된다.
    )
  }
  
  // user를 데이터베이스에 저장하는 부분
}
~~~

~~~kotlin
class User(val id: Int, val name: String, val address: String)

fun saveUser(user: User) {
  fun validate(user: User, value: String, fieldName: String) {
    if(value.isEmpty()) {
      throw IllegalArgumentException(
      	"Can't save user ${user.id}: empty ${fieldName}"
      )
    }
  }
  
  validate(user, user.name, "Name")
  validate(user, user.address, "Address")			// 로컬 함수를 호출해서 각 필드를 검증한다.
}
~~~

- 검증 코드를 로컬 함수로 분리하였다. 그 결과, 중복을 없애고 코드 구조를 깔끔하게 유지한다.
- 중복된 로직은 사라졌고, 필요하면 User의 다른 필드에 대한 검증을 추가하는 것도 간단하다.

~~~kotlin
class User(val id: Int, val name: String, val address: String)

fun saveUser(user: User) {
  fun validate(value: String, fieldName: String) {
    if(value.isEmpty()) {
      throw IllegalArgumentException(
      	"Can't save user ${user.id}: " + "empty ${fieldName}"
      )
    }
  }
}
~~~

- 이제 saveUser의 user 파라미터를 중복으로 사용하지 않고, 바깥 함수의 파라미터에 직접 접근한다.

~~~kotlin
class User(val id: Int, val name: String, val address: String)

fun User.validateBeforeSave()(user: User) {
  fun validate(value: String, fieldName: String) {
    if(value.isEmpty()) {
      throw IllegalArgumentException(
        "Can't save user ${user.id}: empty ${fieldName}"
      )
    }
  }
  
  validate(name, "Name")
  validate(address, "Address")
}

fun saveUser(user: User) {
  user.validateBeforeSave()	// user를 DB에 저장
}
~~~

- 검증 로직을 User 클래스를 확장한 함수로 만드는 것도 가능하다.
- User는 내가 만든 코드 기반에 존재하는 클래스이지만, 해당 경우 검증 로직은 User 이외에서는 쓰이지 않는다.
- User를 간결하게 유지하면 생각해야 할 내용이 줄어들어서 더 쉽게 코드를 파악할 수 있다.
- 확장함수를 로컬함수로 지정할 수 있지만, 깊이가 깊어지면 코드를 읽기 어려워지기 때문에,
  일반적으로 한 단계만 함수를 중첩시킨다. [User.validateBeforeSave를 saveUser 내부에]
