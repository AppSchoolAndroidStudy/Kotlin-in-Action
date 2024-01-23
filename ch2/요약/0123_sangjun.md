## 2.1 기본 요소: 함수와 변수

### 함수

```kotlin
fun max(a: Int, b: Int) : Int {
	return if (a > b) a else b
}
```

- 함수 선언 시 fun 키워드 사용
- fun 함수이름(파라미터: 파라미터 타입) : 반환타입
- 코틀린 if는 문이 아니라 식이다
    - `statement`: 자신을 둘러싸고 있는 가장 안쪽 블록의 최상위 요소로 존재하며 어떠한 값을 생성하지 않다
    - `expression`: 값을 생성하며 다른 표현식의 자식으로 계산에 참여 할 수 있다

```kotlin
fun max(a: Int, b: Int): Int = if (a > b) a else b
```

- `블록이 본문인 함수`: 중괄호로 둘러싸인 함수
- `식이 본문인 함수`: 등호와 표현식으로 구성된 함수
- expression 함수로 표현하면 더 간결하게 사용할 수 있다 → 코틀린에서 expression으로 자주 사용

```kotlin
fun max(a: Int, b: Int) = if (a > b) a else b
```

- 타입 추론: 본문이 표현식인 함수의 경우, 컴파일러가 타입을 분석하고 반환 타입을 자동으로 결정한다
- 블록이 본문인 함수에서는 반환 타입을 명시해야 하며 return 문을 사용하여 반환 값을 지정해야 한다

### 변수

<aside>
💡 코틀린에서는 변수 이름 뒤에 타입을 명시하거나 생략할 수 있다

</aside>

- 코틀린에서는 타입 명시를 생략하는 것이 일반적이다
- expression이 본문인 함수에서와 마찬가지로, 타입을 명시하지 않으면 컴파일러가 초기화 표현식의 타입을 변수 타입으로 자동 지정하기 때문이다
- 따라서 자바처럼 타입을 먼저 쓰고 그 다음에 변수 이름을 쓰면 표현식과 변수 선언을 구분할 수 없게 된다.

```kotlin
val answer = 42
```

- 초기화 식은 42이며, 이는 Int에 해당한다. 따라서 answer 변수는 Int 타입이다.

```kotlin
val answer: Int
answer = 42
```

- 초기화 표현식을 사용하지 않고 변수를 선언하는 경우에는 변수 타입을 명시해야 한다.
- `val`(value에서 유래): 불변 참조를 저장하는 변수.
    - val로 선언된 변수는 초기화된 후 재할당될 수 없다.
    - 자바에서는 final 변수에 해당한다.
- `var`(variable에서 유래): 가변 참조
    - 자바의 일반 변수에 해당한다.
- 기본적으로 `val`키워드를 사용하여 모든 변수를 불변으로 선언하고, 꼭 필요할 때만 var로 변경하세요.

```kotlin
val message: String
if (canPerformOperation()){
    message = "Success" 
    ...
}
else{
    message = "Failed"
}
```

- `val` 변수는 블록을 실행할 때 정확히 한 번 초기화되어야 하지만, 컴파일러가 블록이 실행될 때 단 하나의 초기화 문만 실행될 것임을 보장할 수 있다면, 조건에 따라 여러 가지 다른 값으로 val 값을 초기화할 수 있다.
- if/else에 따라 오직 하나의 초기화 문만 실행되므로 여러 값으로 초기화할 수 있다.
- `val` 참조 자체가 불변이더라도 가리키는 객체의 내부 값은 변경될 수 있다

### 문자열 템플릿

```kotlin
fun main(args: Array<String>) {
	val name = if (args.size > 0) args[0] else "Kotlin"
    println("Hello, $name!")
}
```

- name이라는 변수가 문자열 리터럴 내에서 사용되는 것을 볼 수 있으며, 자바의 문자열 연결 연산("Hllo" + name + "!")처럼 복잡하게 사용할 필요가 없다.
- 문자열 리터럴에서 필요한 곳에 변수를 넣고 변수 앞에 `$`를 추가하기만 하면 된다.
- `$` 문자 자체를 문자열에 넣고 싶다면 `\`를 붙여 이스케이프 시켜주면 된다.

```kotlin
fun main(args: Array<String>) {
	println("Hllo, ${if (args.size > 0) args[0] else "someone"}!")
}
```

- 문자열 템플릿에는 간단한 변수 이름 뿐만 아니라 **복잡한 식**도 `{ }` 로 둘러싸서 넣어줄 수 있다.

## 2.2 클래스와 프로퍼티

```java
public class Person {
	private final String name;

	public Person(String name) {
			this.name = name;
	}

	public String getName() {
			return name;
	}
}
```

```kotlin
clss Person(val name: String)
```

- java 코드를 kotlin으로 변환하면 엄청나게 코드가 줄여드는 것을 확인 할 수 있다
- 이처럼 코드 없이 데이터만 저장하는 클래스를 `값 객체(value object)`라고 부른다

### 프로퍼티

<aside>
💡 클래스의 목적은 데이터를 `캡슐화`하고 캡슐화된 데이터를 처리하는 코드를 한 주체 아래 가두는 것이다

</aside>

- `필드(field)`: 자바에서는 데이터가 필드에 저장되며, 멤버 필드의 가시성은 일반적으로 private이다.
- `접근자 메소드(accessor method)`: 클래스는 클라이언트가 클래스를 사용하여 데이터에 접근할 수 있는 채널로 사용할 수 있는 접근자 메소드를 제공한다.
- `getter & setter`: 일반적으로 필드를 읽기 위한 getter가 제공되며, 필드의 변경이 허용되어야 하는 경우 추가적인 setter를 제공할 수 있다.
- 자바에서는 필드와 그 접근자를 묶어 `프로퍼티`이라고 부른다.
- 코틀린은 `프로퍼티`를 기본 언어 기능으로 제공하여 자바의 `필드`와 `접근자 메소드`를 완전히 대체한다.
- `val` : 읽기 전용 프로퍼티 -> getter
- `var` : 변경 가능한 프로퍼티 -> getter & setter

```kotlin
class Person(
	val name: String,
	var isMarried: Boolean 
)
```

- name: 읽기 전용 프로퍼티 → (비공개)필드, 필드를 읽는 (공개) getter
- isMarried: 쓸 수 있는 프로퍼티 → (비공개)필드, (공개)getter, (공개)setter

### 커스텀 접근자

```kotlin
class Rectangle(val height: Int, val width: Int) {
	val isSquare: Boolean
		get() {
			return height == width
		}
		or
		get() = height == width
}
```

- 프로퍼티의 접근자를 직접 작성할 수도 있다. 매개변수가 없는 함수를 정의하는 것과 사용자 정의 getter를 정의하는 것 중 어느 것이 더 낫다고 말할 수는 없다. 구현이나 성능에서의 차이는 없으며, 오직 가독성에서의 차이만 있다.

### 디렉터리와 패키지

- 코틀린에도 자바와 유사한 `패키지` 개념이 있다. 모든 코틀린 파일의 시작 부분에 패키지 문을 넣을 수 있으며, 그러면 해당 파일의 클래스, 함수, 속성 등 모든 선언이 그 패키지에 배치된다.
    - 다른 파일에 정의된 선언도 같은 `패키지` 안에 있다면 직접 사용할 수 있다.
    - 다른 패키지에 정의된 선언을 사용하려면 `import`를 통해 선언을 가져와야 한다
- 코틀린에서는 `클래스 import`와 `함수 import` 간에 차이가 없다. 모든 선언은 `import` 키워드로 가져올 수 있으며, 최상위 함수는 이름을 사용하여 `import`할 수 다

## 2.3 선택 표현과 처리: enum과 when

### enum 클래스 정의

```kotlin
enum class Color(
	val r: Int, 
	val g: Int, 
	val b: Int
) {
    RED(255, 0, 0), ORANGE(255, 165, 0),
    YELLOW(255, 255, 0), GREEN(0, 255, 0), BLUE(0, 0, 255),
    INDIGO(75, 0, 130), VIOLET(238, 130, 238);

    fun rgb() = (r * 256 + g) * 256 + b
```

- 코틀린에서 `enum`은 enum 클래스 형태로 사용되는 소프트 키워드이다.
- 클래스 앞에서는 특별한 의미를 가지지만, 그 외에는 이름으로 자유롭게 사용할 수 있다. (반면, class는 키워드이며 어디에서든 이름으로 사용될 수 없다.)
- `enum`은 열거 타입을 나타낸다. 하지만 자바와 마찬가지로, 코틀린에서 `enum`은 단순히 값을 열거하는 것이 아니다. enum 클래스 내부에 속성이나 메소드도 정의할 수 있다.
- `enum` 클래스는 일반 클래스처럼 생성자와 속성을 선언한다. 특히 이 코드는 코틀린에서 `세미콜론`이 필요한 유일한 부분을 보여준다.

### when으로 enum 클래스 다루기

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

- 코틀린에는 when이라는 구성 요소가 있다.
- 이것은 자바의 switch를 대체하지만 훨씬 더 강력하며 더 자주 사용된다.
- if처럼 when도 값을 생성하는 표현식이며, 따라서 표현식이 본문인 함수에서 직접 사용될 수 있다.
- 자바와 달리 각 분기의 끝에 break를 넣을 필요가 없다.

### when과 임의의 객체를 함께 사용

```kotlin
fun mix(c1: Color, c2: Color) =
	when (setOf(c1, c2)) {
		setOf(RED, YELLOW) -> ORANGE
		setOf(YELLOW, BLUE) -> GREEN
		setOf(BLUE, VIOLET) -> INDIGO
		else -> throw Exception("Dirty color")
	}
```

- Kotlin의 `when`은 분기 조건에 임의의 객체를 허용한다.

### **인자 없는 when 사용**

- 위의 함수가 호출될 때마다 여러 Set 인스턴스가 생성되어, 함수 인자로 주어진 두 색상이 when의 분기 조건에 있는 다른 두 색상과 같은지 비교한다. 이 함수들이 자주 호출되면 불필요한 가비지 객체가 증가할 수 있으므로 리팩토링이 필요하다.

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

- 이 경우 인자 없는 when 표현식을 사용하면 불필요한 객체 생성을 방지할 수 있으며 코드가 약간 읽기 어려워지지만, 추가적인 객체를 생성하지 않는 것과 같은 더 나은 성능을 가질 수 있다.

### **스마트 캐스트: 타입 검사와 타입 캐스트를 조합**

```kotlin
fun eval(e: Expr): Int {
    if (e is Num) {
        val n = e as Num
        return n.value
    }
    if (e is Sum) {
        return eval(e.right) + eval(e.left) //변수 e에 대해 스마트 캐스트를 사용
    }
    throw IllegalArgumentException("Unknown expression")
}
```

- 코틀린에서는 변수의 타입을 확인하기 위해 `is`를 사용한다.
- 자바에서는 변수의 타입을 확인한 후에 그 타입의 멤버에 접근하기 위해 변수 타입을 명시적으로 캐스팅해야 하지만, 코틀린에서는 프로그래머 대신 컴파일러가 캐스팅을 해준다
- `스마트 캐스트`: 변수가 원하는 타입인지 `is`로 확인하고 나면, 변수를 원하는 타입으로 캐스팅할 필요 없이 처음부터 원하는 타입으로 선언된 것처럼 사용할 수 있다. (이는 컴파일러가 캐스팅을 수행하기 때문이다.)
- `스마트 캐스트`는 변수의 값이 is로 타입 확인된 후 변경될 수 없을 때만 작동한다.
- IDE를 사용할 때 `스마트 캐스트` 부분의 배경색이 다르게 표시되어 자동으로 변환이 이루어졌음을 쉽게 알 수 있다.

```kotlin
val n = e as Num
```

- 원하는 타입으로 명시적으로 타입 캐스팅 하려면 `as` 키워드를 사용한다.

### 리팩토링: if를 when으로 변경

```kotlin
fun eval(e: Expr): Int =
    if (e is Num) {
        e.value
    } else if (e is Sum) {
        eval(e.right) + eval(e.left)
    } else {
        throw IllegalArgumentException("Unknown expression")
    }
```

- 코틀린에서는 자바와 달리 if가 값을 생성하기 때문에 별도의 삼항 연산자가 없다.
- return 문과 중괄호를 제거하고 if 표현식을 본문으로 사용한다

```kotlin
fun eval(e: Expr): Int =
    when (e) {
        is Num ->
            e.value //스마트 캐스트
        is Sum ->
            eval(e.right) + eval(e.left) //스마트 캐스트
        else ->
            throw IllegalArgumentException("Unknown expression")
    }
```

- when을 사용하여 더 깔끔하게 다듬을 수 있다

## 대상을 이터레이션: while과 for 루프

### while 루프

- `while`문은 코틀린과 자바가 동일하다

### 수에 대한 이터레이션:  범위와 수열

```kotlin
fun main(args: Array<String>) {
    for (i in 100 downTo 1 step 2) {
        print(fizzBuzz(i))
    }
}
```

- 코틀린에서 **`for`** 루프는 자바의 향상된 **`for-each`** 루프와 동일하며 주로 범위에서 작동한다.
- 코틀린의 범위는 `'..'`연산자를 사용하여 시작 값과 끝 값으로 정의되는 값의 간격(일반적으로 숫자)이다. 중요한 점은 코틀린 범위에서는 최종 값이 항상 포함된다는 것이다.
- 코틀린은 범위를 사용하여 초기 값, 증분, 최종 값과 같은 일반적인 반복 시나리오를 단순화한다. 더 복잡한 반복을 위해 코틀린은 추가 기능을 제공한다.
    - **downTo**: 수열 감소에 사용된다.
    - **step**: 순서대로 숫자를 건너뛸 수 있다.
    - **역방향 수열**: 음수 증분을 사용하면 역순으로 반복할 수 있다.
- **`until`** 함수를 사용하여 종료 값을 제외하는 반 개방 범위를 생성하는 방법을 제공한다. 이는 최종 값을 포함하지 않고 최대 반복해야 할 때 유용하다.

### 맵에 대한 이터레이션

```kotlin
val binaryReps = TreeMap<Char, String>()
for (c in ‘A’ .. ‘F’) { 
    val binary = Integer.toBinaryString(c.toInt())
    binaryReps[c] = binary
}

for ((letter, binary) in binaryReps) { 
    println(“$letter = $binary”)
}
```

- `for .. in` 루프는 종종 컬렉션을 반복하는 데 사용된다
- `..` 연산자가 숫자형 타입 값뿐만 아니라 문자형 타입 값에도 적용될 수 있음을 알 수 있다

### **in으로 컬렉션이나 범위의 원소 검사**

```kotlin
fun recognize(c: Char) = when (c) {
    in '0'..'9' -> "It's a digit!"
    in 'a'..'z', in 'A'..'Z' -> "It's a letter!"
    else -> "I don't know…"
}
```

- 범위 내에 값이 있는지 확인하기 위해 `in` 연산자를 사용할 수 있다.
- 반대로, 범위 밖에 값이 있는지 확인하기 위해 `!in` 연산자를 사용할 수 있다.

## 코틀린의 예외 처리

### try, catch, finally

```kotlin
fun readNumber (reader: BufferedReader): Int? { 
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

- 자바 코드와 가장 큰 차이는 `throws` 절이 코드에 없다.

### try를 식으로 사용

```kotlin
fun readNumber (reader: BufferedReader) {
     val number = try {
        Integer.parseInt(reader.readLine())
     } catch (e: NumberFormatException) {
        return
    }
    println(number)
}
```