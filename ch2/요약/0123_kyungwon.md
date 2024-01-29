------

## [1] 기본요소 : 함수와 변수

#### (1) Hello World   

```kotlin
fun main(args: array<String>) {
    println("Hello World")
}
```

해당 코드를 보고 알 수 있는 사실
* 함수를 최상위 수준에 정의 가능
* 자바와 달리 배열 처리를 위한 문법 존재 X
* ;(Semicolon)의 불필요
* 여러가지 표준 라이브러리 함수들의 간결화

* * *
#### (2) 함수

~~~kotlin
fun max(a: Int, b: Int): Int {
    return if (a > b) a else b
}
~~~
| Kotlin         | Statement와 Expression의 구분                                |
| :------------- | ------------------------------------------------------------ |
| 문(Statement)  | 자신을 둘러싸고 있는 안쪽 블록의 최상위 요소로 존재함<br/>아무런 값을 만들어 내지 않음<br />Java의 일반적인 제어구조 |
| 식(Expression) | 값을 만들어내며, 다른 식의 하위 요소를 계산에 참여 가능<br />kotlin의 일반적인 제어구조 |

[식이 본문인 함수] 

~~~kotlin
fun max(a: Int, b: Int): Int { return if (a > b) a else b }
~~~

~~~kotlin
fun max(a: Int, b: Int): Int = return if (a > b) a else b
~~~

~~~kotlin
fun max(a: Int, b: Int) = if(a > b) a else b
~~~

- Kotlin은 정적 타입 지정 언어이므로, 컴파일 시점에 모든 식의 타입을 지정해야 하며, 반환타입이 정해져야 한다.

- 하지만, 식이 본문인 함수(등호와 식으로 이루어진 함수)의 경우에는 사용자가 타입을 적지 않아도, 컴파일러가 정해준다.

- 반대로 블록이 본문인 함수는 반드시 반환 타입을 지정해야 하고, return 문을 사용하여 반환 값을 명시해야 함.

[IntelliJ 사용 시]

인텔리제이 IDEA 사용 시, 함수를 두 가지 방식으로 나누어서 변환할 수 있다.

- Convert to Block Body : 분문이 중괄호로 이루어진 '블록이 본문인 함수' 로 변환
- Convert to expression Body : 본문이 등호와 식으로 이루어진 '식이 본문인 함수'로 변환

* * *

#### (3) 변수

Kotlin에서는 키워드로 변수선언을 시작하는 대신, 변수의 이름 뒤에 타입을 명시하거나 생략하도록 허용한다.

~~~kotlin
val question = "삶, 우주, 그리고 모든 것에 대한 궁극적인 질문"
val answer: Int = 42 // 해당 변수 선언 시, 타입 표기 생략 가능
val yearsToCompute = 7.5e6 // 부동소수점 상수 사용 시, Double로 변경 (7.5 * 10^6)
~~~

[val 과 var]

- val
  - 변경 불가능한 참조를 저장하는 변수
  - 초기화 하고 나면 재대입이 불가
  - 참조가 가르키는 객체의 내부 값은 변경 가능
  - Java의 final 변수의 역할과 동일
- var
  - 변경 가능한 참조
  - 값은 변경할 수 있으나, 타입이 변경되서는 안됨
  - Java의 일반 변수의 역할과 동일

------

#### (4) 문자열 템플릿

~~~kotlin
fun main(args: Array<String>) {
  val name = if (args.size > 0) args[0] else "Kotlin"
  println("Hello, $name!")
}
~~~

- Kotlin은 변수를 문자열 안에 사용할 수 있다. 

- 문자열 리터럴의 필요한 곳에 변수를 넣되 변수 앞에 $ 표시를 추가해야 한다.

-  Java 문자열 접합 연산을 사용하여 효율적으로 표시 가능하다. ("Hello, " + name + "!")

- 한글의 경우, 문자열 템플릿을 사용 시, 유니코드 변수 이름과 충돌의 우려가 있으므로, 변수를 중괄호로 감싸야 한다.

------

## [2] 클래스와 프로퍼티

#### (1) 프로퍼티

~~~kotlin
class Person(
	val name: String, 			// 읽기전용 프로퍼티 > (공개)Getter
  var isMarried: Boolean	// 사용 가능한 프로퍼티 > (비공개)Field, (공개)Getter, (공개)Setter
)
~~~

- 프로퍼티 = 필드 + 접근자 메서드
- 클래스 : 데이터를 캡슐화(Encapsulation) 하여 한 주체 안에 가두어서 필드에 저장
- 접근자 메서드 : 자신이 사용하는 클라이언트가 데이터에 접근할 수 있도록 하는 통로 [Getter, Setter]

> Kotlin은 값을 저장하기 위한 비공개 **필드**, 그 필드에 값을 저장하기 위한 **세터**, 
> 값을 일긱 위한 **게터**로 이루어진 접근자 구현을 제공한다.

```kotlin
val person = Person("Bob", true) // new Keyword를 사용하지 않아도 생성자 호출 가능
println(person.name)	// 프로퍼티 이름을 직접 사용해도 Getter 호출 가능
println(person.isMarried)
```

- Kotlin에서 위의 Person 클래스를 다음과 같이 사용할 수 있다.

------

#### (2) 커스텀 접근자

~~~kotlin
class Rectangle(val height: Int, val width: Int) {
  val isSquare: Boolean
  get() { return height == width}
}
~~~

- 해당 클래스처럼 프로퍼티의 접근자를 직접 장성하는 것이 가능함.
- isSquare 프로퍼티는 자체 값을 저장하는 필드가 따로 필요 없음.
- 해당 접근자를 Java에서 사용하려면 isSquare 메서드를 호출하면 됨.

~~~kotlin
val rectangle = Rectangle(41, 43)
println(rectangle.isSquare)	// false
~~~

------

#### (3) 디렉터리와 패키지

- Kotlin 파일의 맨 앞에 package문을 넣어서 해당 파일 안의 모든 선언(클래스, 함수, 프로퍼티 등)이 해당 함수에 들어간다.
- 같은 패키지에 속해 있다면 다른 파일에 정의한 선언일지라도 사용 가능
- 다른 패키지에 속해 있다면 임포트를 통해 선언 가능
- 패키지 안에 *을 추가하면 패키지 안의 모든 선언을 임포트 할 수 있음.

~~~kotlin
package geometry.example
import geometry.shapes.createRandomRectangle

fun main(args: Array<String>) {
  println(createRandomRectangle().isSquare)		// 다른 패키지에 있는 함수 임포트
}
~~~

- Kotlin에서는 여러 클래스를 한 파일에 넣을 수도 있고, 파일의 이름도 마음대로 정할 수 있다.
- 디스크 상의 어느 디렉터리에 소스코드가 있어도 관계없어서, 원하는 대로 소스코드를 구성하는 것이 가능하다.
- 하지만, Java의 방식을 따르지 않았을 때는, Java 클래스를 Kotlin 클래스로 마이그레이션 할 때 문제가 생길 수 있다.

------

## [3] enum과 when [선택 표현과 처리]

#### (1) enum class

```kotlin
enum class Color {
  RED, ORANGE, YELLOW, GREEN, BLUE, INDIGO, VIOLET
}
```

- Kotlin에서 enum은 소프트 키워드라 부르는 존재이다. 이는 Java에서 보다 더 많은 키워드를 써야 한다.
- 자바와 달리 값을 열거만 하는 것이 아닌, enum class안에 프로퍼티나 메서드를 정의할 수 있다.

~~~kotlin
enum class Color(val r: Int, val g: Int, val b: Int) {	// 상수의 프로퍼티 정의
  RED(255, 0, 0),						// 각 상수를 생성할 때 그에 대한 프로퍼티 값을 지정한다.
  ORAGNE(255, 165, 0),
  YELLOW(255, 255, 0),
  GREEN(0, 255, 0),
	BLUE(0, 0, 255),
	INDIGO(75, 0, 130),
	VIOLET(238, 130, 238);		// 해당 부분에서는 세미콜론이 필수이다.
  
  fun rgb() = (r * 256 + g) * 256 + b	// enum class 안에서 aptjem
}
~~~

---

#### (2) when으로 enum class 다루기

~~~kotlin
fun getMnemonic(color: Color) =      // 함수의 반환 값으로 when 식을 직접 사용
    when (color) {	
        Color.RED, Color.ORANGE, Color.YELLOW -> "Warm"
      	Color.GREEN -> "Netural"
      	Color.BLUE, Color.INDIGO, Color.VIOLET -> "Cold"
    }
~~~

- Java와 달리 각 분기에 끝에 Break를 넣지 않아도 오류가 생기지 않음
- 한 분기 안에서 여러 적으로 매치 패턴을 사용할 때는 , 를 사용하여 분리
- 상수 값을 임포트 하면 해당 코드를 더 간단하게 만들 수 있음.

~~~kotlin
import ch02.colors.Color
import ch02.colors.Color.*

fun getMnemonic(color: Color) =
    when (color) {	
        RED, ORANGE, YELLOW -> "Warm"
      	GREEN -> "Netural"
      	BLUE, INDIGO, VIOLET -> "Cold"
    }
~~~

***

#### (3) when과 임의의 객체 함께 사용

~~~kotlin
fun mix(c1: Color, c2: Color) = when(setOf(c1, c2)) {
  setOf(RED, YELLOW) -> ORANGE						// 두 색을 혼합해서 다른 색을 만들 수 있는 경우 열거
  setOf(YELLOW, BLUE) -> GREEN
  setOf(BLUE, VIOLET) -> INDIGO
  else -> throw Exception("Dirty Color")	// 매치되는 분기 조건이 존재하지 않을 시
}
~~~

~~~kotlin
println(mix(BLUE, YELLOW))	// GREEN
~~~

- Java의 switch와 달리, Kotlin의 when은 임의의 객체를 허용한다.

- 인자로 전달받은 여러 객체를 그 객체들을 포함하는 집합인 Set 객체로 만드는 setOf 함수가 존재함. 이는 순서는 상관없음

***

#### (4) 인자 없는 when 사용

~~~kotlin
fun mix(c1: Color, c2: Color) =
    when {	// when에 인자가 존재하지 않음
        (c1 == RED && c2 == YELLOW) || (c1 == YELLOW && c2 == RED) -> ORANGE
        (c1 == YELLOW && c2 == BLUE) || (c1 == BLUE && c2 == YELLOW) -> GREEN
      	(c1 == BLUE && c2 == VIOLET) || (c1 == VIOLET && c2 == BLUE) -> INDIGO 

        else -> throw Exception("더러운 색깔")
    }
~~~

- 임의의 객체와 함께 when을 사용하는 경우, 호출될 때마다 주어진 두 색이 when의 분기 조건에 있는 다른 두 색과 같은지
  비교하기 위해서 여러 set 인스턴스를 생성하기 때문에 비효율적임
- when에 아무 인자가 없다면 각 분기의 조건이 Boolean 결과를 계산하는 식이어야 함.

***

#### (5) 스마트 캐스트

```kotlin
interface Expr
class Num(val value: Int): Expr	// value라는 프로퍼티만 존재하는 클래스. Expr 인터페이스 구현
class Sum(val left: Expr, val right.Expr): Expr
```

~~~kotlin
fun eval(e: Expr): Int {
  if(e is Num) {
    val n = e as Num
    return n.value
  }
  if(e is Sum) {
    return eval(e.right) + eval(e.left)	// 변수 e에 대한 스마트 캐스트
  }
  throw IllegalArgumentException("Unknown Expression")
}
~~~

```kotlin
println(eval(Sum(Sum(Num(1), Num(2)), Num(4))))	// 7
```

- Kotlin에서 is를 이용하여 변수 타입을 검사하고 나면 컴파일러가 원하는 변수 타입으로 캐스팅 해준다.
- 클래스의 프로퍼티에서 스마트캐스트를 사용한다면 그 프로퍼티는 반드시 val 이어야 하며, 커스텀 접근자를 사용할 수 없다.
- 원하는 타입으로 명시적으로 타입 캐스팅을 해주기 위해서는 as 키워드를 사용한다.

***

#### (6) if를 when으로 변경 [Refactoring]

```kotlin
fun eval(e: Expt): Int = 
	when(e) {
		is Num -> e.value		// 인자 타입을 검사한 후, 스마트 캐스팅이 사용됨.
    is Sum -> eval(e.right) + eval(e.left)
    else -> throw IllegalArgumentException("Unknown Expression")
	} 
```

- when 식은 동등성 검사가 아닌 받은 값의 타입을 검사하는 역할로도 사용할 수 있다.
- 이는 타입을 검사하고 나면 바로 스마트 캐스팅이 이뤄지며, 따라서 Num이나 Sum의 멤버에 접근할 때 캐스팅이 필요없다.

***

#### (7) if와 when의 분기에서 블록 사용

~~~kotlin
fun evalWithLogging(e: Expr): Int {
    return when (e) {
        is Num -> {
            println("num: ${e.value}")
            e.value		// e의 타입이 Num인 경우에 e.value가 반환된다.
        }
        is Sum -> {
            val left = evalWithLogging(e.left)
            val right = evalWithLogging(e.right)
            println("sum: $left + $right")
            left + right	// e의 타입이 Sum인 경우에 해당 식의 값이 반환된다.
        }
        else -> throw IllegalArgumentException("Unknown Expression")
    }
}
~~~

- eval 함수에 로그를 추가시키고 싶다면 각 분기를 블록으로 만들고, 블록의 맨 마지막에 분기의 결과 값을 위치시키면 된다.

***

## [4] 대상을 이터레이션: while과 for 루프

#### (1) while 루프

- Kotlin에는 while 루프와 do-while 루프가 존재함.
- 해당 루프는 매우 단순하며, 코틀린에서 추가한 기능 조차 없음

***

#### (2) 수에 대한 이터레이션: 범위와 수열

- Kotlin에서는 범위(Range)를 사용하여 루프를 표현한다.
- 어떤 범위에 속한 값을 일정한 순서로 이터레이션하는 경우를 **수열**이라고 부른다.

~~~kotlin
fun fizzBuzz(i: Int) = when {
  i % 15 == 0 -> "FizzBuzz"
  i % 3 == 0  -> "Fizz"
  i % 5 == 0  -> "Buzz"
  else -> "$i"
}
~~~

~~~kotlin
for(i in 1..100) {
  print(fizzBuzz(i))
}
// 1 2 Fizz 4 Buzz Fizz 7 ...
~~~

```kotlin
for(i in 100 downTo 1 step 2) {
  print(fizzBuzz(i))
}
// Buzz 98 Fizz 94 92 FizzBuzz 88 ...
```

- 증가 값 step을 갖는 수열에 대해서 이터레이션한 모습이다.
- 증가 값을 사용하면 수를 건너뛸 수 있으며, 증가 값을 음수로 만들면 역방향 수열을 만들 수 있다.
- '..' 는 항상 범위의 끝 값을 포함한다. 그 외로, 닫힌 범위에 대해서 이터레이션 하기 위해서는 until 함수를 사용하면 된다.

***

#### (3) 맵에 대한 이터레이션

~~~kotlin
val binaryReps = TreeMap<Char, String>()			// Key에 대해 정렬하기 위해 TreeMap 사용

for(c in 'A'..'F') {
  val binary = Integer.toBinaryString(c.toInt()) // ASCII code -> 2진 표현
  binaryReps[c] = binary // c를 Key로 c의 2진 표현을 맵에 넣는다.
} 

for((letter, binary) in binaryReps) {
  println("$letter = $binary")	// Map의 Key와 값을 두 변수에 대입(Map에 대한 이터레이션)
}
~~~

- '..' 연산자는 숫자 타입의 값 뿐만 아니라 문자 타입에 대한 값에도 적용할 수 있다.
- 해당 코드에서는 get, put을 사용하는 대신, map[key]나 map[key] = value를 사용해 값을 가져오고 설정할 수 있다.

<br/>

```kotlin
val list = arrayListOf("10", "11", "1001")

for((index, element) in list.withIndex()) {	// 인덱스와 함께 컬렉션을 이터레이션
  println("$index : $element")
}

// 0 : 10 
// 1 : 11
// 2 : 1001
```

- 맵이 아닌 컬렉션에서도 활용할 수 있다.
- 원소의 현재 인덱스를 유지하면서 컬렉션을 이터레이션하는 것이 가능하다.

***

#### (4) in으로 컬렉션이나 범위의 원소 검사

~~~kotlin
fun recognize(c: Char) = when(c) {
  in '0'..'9' -> "It's a digit"
  in 'a'..'z' -> "It's a letter"
  else -> "I don't know"
}
~~~

- 비교가 가능한 클래스라면 해당 클래스의 인스턴스 객체를 사용하여 범위를 만들 수 있다.

~~~kotlin
println("Kotlin" in "Java".."Scala")	// true
println("Kotlin" in setOf("Java", "Scala"))	// false
~~~

***

## [5] Kotlin의 예외 처리

코틀린의 예외처리는 Java나 다른 언어와 비슷한다.

함수는 정상적으로 종료할 수 있지만, 오류가 발생하면 예외를 던질 수 있다.

~~~kotlin
val percentage = 
	if(number in 0..100) number
	else throw IllegalArgumentException {
    "A percentage value must be between 0 and 100: $number"
  }
~~~

#### (1) try, catch, finally

```kotlin
fun readNumber(reader: BufferedReader()): Int? {
  try {
    val line = reader.readLine()
    return Integer.parseInt(line)
  } 
  catch(e: NumberFormatException) {
    return null
  } 
  finally {
    reader.close()
  }
}
```

```kotlin
val reader = BufferedReader(StringBuffer("239"))
println(readNumber(reader))
// 239
```

- Java 코드와 가장 큰 차이는 thows절이 코드에 존재하지 않는다.
- Kotlin에서는 함수가 던지는 예외를 지정하지 않고 발생한 예외를 잡아내도 되고 잡아내지 않아도 된다.
- Java7의 자원을 사용하는 try-with-resource는 Kotlin에서는 문법을 제공하지 않는다.

***

#### (2) try를 식으로 사용

~~~kotlin
fun readNumber(reader: BufferedReader) {
  val number = try {
    Integer.parseInt(reader.readLine())	// 예외가 발생하지 않으면 이 값을 사용
  } catch(e: NumberFormatException) {
    null	// 예외가 발생하면 null 값을 사용
  } 
  println(number)
}
~~~

```kotlin
val reader = BufferedReader(StringBuffer("not a number"))
readNumber(reader)
// null
```

- Kotlin의 try 키워드는 식이므로, 값을 변수에 대입할 수 있다.
- try 코드 블록이 정상적으로 끝나면 그 블록의 마지막 식의 값이 결과다.

***

