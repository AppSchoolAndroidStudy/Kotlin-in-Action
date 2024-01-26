# 2 코틀린 기초
- 함수, 변수, 클래스 enum, 프로퍼티를 선언하는 방법
- 제어 구조
- 스마트 캐스트
- 예외 던지기와 예외 잡기

<br>

## 2.1 기본 요소: 함수와 변수 

> 코틀린은 변경 가능한 데이터보다 변경할 수 없는 불변 데이터 사용을 장려

<br>

### 2.1.1 Hello, World!

```kotlin
fun main(args: Array<String>) {
    println("Hello, world!")
}
```

- 자바와 달리 꼭 클래스 안에 함수를 넣어야 할 필요가 없음
- 자바와 달리 배열 처리를 위한 문법이 따로 존재하지 않음

<br>

### 2.1.2 함수

- 블록이 본문인 함수 : 본문이 중괄호로 둘러싸인 함수

```kotlin
fun max(a: Int, b: Int): Int {
    return if (a > b) a else b
}
```
- 식이 본문인 함수 : 등호와 식으로 이뤄진 함수

```kotlin
fun max(a: Int, b: Int): Int = if (a > b) a else b

// 반환 타입 생략 가능
fun max(a: Int, b: Int) = if (a > b) a else b
```

**타입 추론**

: 컴파일러가 타입을 분석해 프로그래머 대신 프로그램 구성 요소의 타입을 정해주는 기능

<br>

### 2.1.3 변수

> 💡 기본적으로는 모든 변수를 val 키워드를 사용해 불변 변수로 선언하고, 나중에 꼭 필요할 때만 var로 변경

```kotlin
val answer = 2

// 초기화 식을 사용하지 않고 변수를 선언할 때는 변수 타입을 반드시 명시해야함
val answer: Int
```

- **val 과 var**

| val(value) | var(variable) |
| --- | --- |
| 변경 불가능한 참조를 저장하는 변수 | 변경 가능한 참조 |
| 초기화하고 나면 재대입이 불가능 | 변수의 값 바뀔 수 있음 |
| 자바로 말하자면 final 변수에 해당 | 자바의 일반 변수에 해당 |

val 참조 자체는 불변일지라도 그 참조가 가리키는 객체의 내부 값은 변경될 수 있음

```kotlin
val languages = arrayListOf("Java") // 불변 참조를 선언
languages.add("Kotlin") // 참조를 가리키는 객체 내부를 변경
```
<br>

## 2.2 클래스와 프로퍼티

### 2.2.1 프로퍼티(property)

- 클래스의 목적은 데이터를 캡슐화하고 캡슐화한 데이터를 다루는 코드를 한 주체 아래 가두는 것
- **자바**
    
    : 데이터를 `field`에 저장하며, 멤버 `field`의 가시성은 보통 `private`이다
    
    : 필드와 접근자를 한데 묶어 `프로퍼티`라고 부름
    
- **코틀린**
    
    : 프로퍼티를 언어 기본 기능으로 제공
    
    : 코틀린 프로퍼티는 자바의 필드와 접근자 메서드를 완전히 대신함
    

```kotlin
class Person(
    // 읽기 전용 프로퍼티
    // (비공개) field, 필드를 읽는 단순한 (공개) getter
    val name: String,
    // 쓸 수 있는 프로퍼티
    // (비공개) field, (공개) getter & setter
    var isMarried: Boolean 
)
```

**Person 클래스 사용**

- 자바

```java
Person person = new Person("Bob", true)
System.out.println(person.getName());
System.out.println(person.isMarried());
```

- 코틀린

```kotlin
val person = Person("Bob", true) // new 키워드를 사용하지 않고 생성자를 호출
println(person.name) // 프로퍼티 이름을 직접 사용 -> 자동으로 게터 호출 해줌
println(person.isMarried)
```

<br>

### 2.2.2 커스텀 접근자

```kotlin
class Rectangle(val height: Int, val width: Int) {
    val isSquare: Boolean
        get() { // 프로퍼티 게터 선언
            return height == width
        }
}
```

- 파라미터가 없는 함수를 정의하는 방식 vs 커스텀 게터를 정의하는 방식
    
    : 구현이나 성능의 차이 X, 가독성 차이만 O
    
<br>

### 2.2.3 디렉터리와 패키지

- 같은 패키지에 속해 있다면 다른 파일에서 정의한 선언일지라도 직접 사용가능
- 다른 패키지에 정의한 선언을 사용하려면 import를 통해 선언을 불러와야 함

<br>

## 2.3 선택 표현과 처리: enum과 when

### 2.3.1 enum 클래스 정의

```kotlin
enum class Color {
    RED, 
    ORANGE, 
    YELLOW, 
    GREEN, 
    BLUE, 
    INDIGO, 
    VIOLET,
}
```

- 코틀린에서 enum은 `soft keyword`라 부르는 존재
- enum은 class 앞에 있을 때는 특별한 의미를 지니지만 다른 곳에서는 이름에 사용
(반면 class는 키워드, class라는 이름을 사용할 수 없음)
- 자바와 마찬가지로 enum은 단순한 값만 열거하는 존재
- enum 클래스 안에도 프로퍼티나 메서드를 정의할 수 있음

```kotlin
enum class Color(
    val r: Int, // 상수의 프로퍼티를 정의함
    val g: Int, 
    val b: Int,
) {
    RED(255, 0, 0), 
    ORANGE(255, 165, 0),
    YELLOW(255, 255, 0),
    GREEN(0, 255, 0), 
    BLUE(0, 0, 255),
    INDIGO(75, 0, 130), 
    VIOLET(238, 130, 238); // 반드시 세미콜론 사용

		// enum 클래스 안에서 메서드를 정의
    fun rgb() = (r * 256 + g) * 256 + b 
}
```
<br>

### 2.3.2 when으로 enum 클래스 다루기

- when
    
    : if 문과 마찬가지로 when도 값을 만들어내는 식
    
    : 자바와 달리 각 분기의 끝에 `break`를 넣지 않아도 됨
    

```kotlin
fun getMnemonic(color: Color) =
    when (color) { 
        Color.RED -> "Richard"
        Color.ORANGE -> "Of"
        Color.YELLOW -> "York"
        Color.GREEN -> "Gave"
        Color.BLUE -> "Battle"
        Color.INDIGO -> "In"
        Color.VIOLET -> "Vain"
    }
```
<br>

### 2.3.3 when과 임의의 객체를 함께 사용

- 코틀린 when은 자바의 `switch`와 비슷하지만 더 강력
    
    : 코틀린 when은 분기 조건은 임의의 객체를 허용 (자바 switch는 분기 조건에 상수만을 사용할 수 있음)
    

```kotlin
fun mix(c1: Color, c2: Color) =
    when (setOf(c1, c2)) {
        setOf(RED, YELLOW) -> ORANGE
        setOf(YELLOW, BLUE) -> GREEN
        setOf(BLUE, VIOLET) -> INDIGO
        else -> throw Exception("Dirty color")
    }
```
<br>

### 2.3.4 인자 없는 when 사용

위 함수는 호출될 때마다 여러 Set 인스턴스를 생성

→ 이 함수가 아주 자주 호출된다면 `불필요한 가비지 객체`가 늘어나는 것을 방지하기 위해 함수를 고쳐쓰는 편이 나음

- 따라서 아래 함수처럼 `인자가 없는 when 식`을 사용하면 불필요한 객체 생성을 막을 수 있음
    
    : 코드가 읽기 어려워지지만 성능 향상을 위해 그 정도 비용을 감수해야하는 경우도 자주 있음
    

```kotlin
fun mixOptimized(c1: Color, c2: Color) =
    when {
        (c1 == RED && c2 == YELLOW) ||
        (c1 == YELLOW && c2 == RED) ->
            ORANGE
    
        (c1 == YELLOW && c2 == BLUE) ||
        (c1 == BLUE && c2 == YELLOW) ->
            GREEN
    
        (c1 == BLUE && c2 == VIOLET) ||
        (c1 == VIOLET && c2 == BLUE) ->
            INDIGO
    
        else -> throw Exception("Dirty color")
    }
```

→ 위 코드는 추가 객체를 만들지 않는다는 장점이 있지만 가독성은 떨어짐

<br>

### 2.3.5 스마트 캐스트: 타입 검사와 타입 캐스트를 조합

```kotlin
fun eval(e: Expr): Int {
    if (e is Num) {
        val n = e as Num
        return n.value
    }
    if (e is Sum) {
        return eval(e.right) + eval(e.left) // 변수 e에 대해 스마트 캐스트를 사용
    }
    throw IllegalArgumentException("Unknown expression")
}
```

- 코틀린에서는 is를 사용해 변수 타입을 검사
- is 검사는 자바의 instanceof와 비슷   
  -  하지만 자바에서 어떤 변수의 타입을 instanceof로 확인한 다음에 그 타입에 속한 멤버에 접근하기 위해서는 명시적으로 변수 타입을 캐스팅 해야함
  - 코틀린에서는 컴파일러가 캐스팅 해줌 → is로 검사하고 나면 그 변수가 원하는 타입으로 선언된 것처럼 사용할 수 있음
    
      → 이를 `스마트 캐스트`라고 부름
- 스마트 캐스트는 val에만 사용 가능
- 원하는 타입으로 명시적으로 타입 캐스팅하려면 as 키워드를 사용

<br>

### 2.3.6 리팩토링: if를 when으로 변경

- 코틀린에서 3항 연산자가 없는 이유?
    - if가 값을 만들어 내기 때문에 자바와 달리 3항 연산자가 필요 없음
    - 코틀린의 `if (a > b) a else b` 는 자바의 `a > b ? a : b` 처럼 작동

```kotlin
fun eval(e: Expr): Int =
    when (e) {
        is Num ->
            e.value // 스마트 캐스트 쓰임
        is Sum ->
            eval(e.right) + eval(e.left) // 스마트 캐스트 쓰임
        else ->
            throw IllegalArgumentException("Unknown expression")
    }
```

<br>

### 2.3.7 if와 when의 분기에서 블록 사용
```kotlin
fun evalWithLogging(e: Expr): Int =
    when (e) {
        is Num -> {
            println("num: ${e.value}")
            e.value
        }
        is Sum -> {
            val left = evalWithLogging(e.left)
            val right = evalWithLogging(e.right)
            println("sum: $left + $right")
            left + right
        }
        else -> throw IllegalArgumentException("Unknown expression")
}
```
<br>

## 2.4 대상을 이터레이션: while과 for 루프

### 2.4.1 while 루프

```kotlin
// 조건이 참인 동안 본문을 반복 실행
while (조건) {
    ...
}
```

```kotlin
// 맨 처음에 무조건 본문을 한 번 실행한 다음, 조건이 참인 동안 본문을 반복 실행
do {
    ...
} while (조건)
```

<br>

### 2.4.2 수에 대한 이터레이션: 범위와 수열

- 컬렉션에 대한 이터레이션을 위해 for .. in 루프를 자주 씀
- 어떤 범위에 속한 값을 일정한 순서로 이터레이션 하는 경우를 **수열**이라고 함
```kotlin
fun fizzBuzz(i: Int) = when {
    i % 15 == 0 -> "FizzBuzz"
    i % 3 == 0 -> "Fixx"
    i % 5 == 0 -> "Buzz"
    else -> "$i"
}

fun main() {
    // 증가 값 step을 갖는 수열에 대해 이터레이션함
    // 100 downTo 1은 역방향 수열을 만듬
    for (i in 100 downTo 1 step 2) {
        print(fizzBuzz(i)
    }	
}
```

<br>

### 2.4.3 맵에 대한 이터레이션

```kotlin
// TreeMap : 키에 대해 정렬
val binaryReps = TreeMap<Char, String>()

for (c in 'A'..'F') {
    val binary = Integer.toBinaryString(c.toInt())  // 아스키 코드를 2진 표현으로 바꿈
    binaryReps[c] = binary
}

for ((letter, binary) in binaryReps) {
    println("$letter = $binary"
}
```

<br>

### 2.4.4 in으로 컬렉션이나 범위의 원소 검사

- `in` : 어떤 값이 범위에 속하는지 검사
- `!in` : 어떤 값이 범위에 속하지 않는 지 검사

```kotlin
fun isLetter(c: Char) = c in 'a'..'z' || c in 'A'..'Z'
fun isNotDigit(c: Char) = c !in '0'..'9'
```

**when에서 in 사용하기**

```kotlin
fun recognize(c: Char) = when (c) {
    in '0'..'9' -> "It's a digit!"
    in 'a'..'z', in 'A'..'z' -> "It's a letter!"
    else -> "I don't know..."
}
```

- 범위는 문자에만 국한되지 않음
    
    : 비교 가능한 클래스라면 그 클래스의 인스턴스 객체를 사용해 범위를 만들 수 있음
    
<br>

## 2.5 코틀린의 예외 처리

```kotlin
val percentage = 
    if (number in 0..100)
        number
    else
        throw IllegalArgumentException( // throw는 식이다
            "A percentage value must be between 0 and 100: $number")
```

<br>

### 2.5.1 try, catch, finally

```kotlin
fun readNumber(reader: BufferedReader): Int? {
    try {
        val line = reader.readLine()
        return Integer.parseInt(line)
    }
    catch (e: NumberFormatException) {
        return null
    }
    finally {
        reader.close()
    }
}
```

- 자바와 가장 큰 차이는 throws 절이 코드에 없다는 점
- 코틀린에서는 함수가 던지는 예외를 저장하지 않고 발생한 예외를 잡아내도 되고 잡아내지 않아도 됨

<br>

### 2.5.2 try를 식으로 사용

```kotlin
fun readNumber(reader: BufferedReader) {
    val number = try {
        Integer.parseInt(reader.readLine())
    } catch (e: NumberFormatException) {
          null
    }
    println(number)
}
```

- 코틀린의 `try` 키워드는 if나 when과 마찬가지로 식임
