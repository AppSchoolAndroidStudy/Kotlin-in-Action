## [1] 람다 식과 멤버 참조

- 람다는 기본적으로 다른 함수에 넘길 수 있는 작은 코드 조각을 뜻한다.
- 이를 사용하면 쉽게 공통 코드 구조를 라이브러리 함수로 뽑아내는 것이 가능하다.

***

#### (1) 람다 소개: 코드 블럭을 함수 인자로 넘기기

~~~java
button.setOnClickListener(new OnClickListener) {
  @Override
  public void onClick(View view) {
    /* 버튼을 클릭 시 실행 할 동작 */
  }
}
~~~

~~~kotlin
button.setOnClickListener { /* 버튼을 클릭 시 실행 할 동작*/ }
~~~

- 함수형 프로그래밍에서는 함수를 하나의 값 처럼 다루어서 번거로운 문제들을 해결한다.
- 클래스를 선언하고 인서턴스를 함수에 넘기는 대신, 함수를 직접 다른 함수에 전달한다.
- 람다 식을 사용하면, 함수를 선언할 필요가 없고, 코드 블록을 직접 함수의 인자로 전달하는 것이 가능하다.
- 해당 예제들은 람다를 하나뿐인 무명 객체 대신 사용할 수 있다는 사실을 보여준다.

***

#### (2) 람다와 컬렉션

~~~kotlin
data class Person(val name: String, val age: Int)
~~~

~~~kotlin
fun findTheOldest(people: List<Person>) {
  var maxAge = 0									// 가장 많은 나이 저장
  var theOldset: Person? = null		// 가장 연장자인 사람 저장
 	for(person in people) {
    if(person.age > maxAge) {
      maxAge = person.age					// 현재까지 발견한 최연장자보다 더 나이가 많으면 변경
      theOldest = person
    }
  }
  println(theOldest)
}

val people = listOf(Person("Alice", 29), Person("Bob", 31))
findTheOldest(people)
// Person(name=Bob, age=31)
~~~

~~~kotlin
val people = listOf(Person("Alice", 29), Person("Bob", 31))
println(people.maxBy { it.age })		// 나이 프로퍼티를 비교하여 가장 값이 큰 원소 찾기
// Person(name=Bob, age=31)
~~~

~~~kotlin
people.maxBy(Person::age)
~~~

- 코드에서 중복을 제거하는 것은 스타일 개선을 위한 중요한 방법 중 하나이다.
- maxBy는 가장 큰 원소를 찾기 위해 비교에 사용할 값을 돌려주는 함수를 인자로 받는다.
- { it.age }는 바로 비교에 사용할 값을 돌려주는 함수이다.

***

#### (3) 람다 식의 문법

~~~kotlin
val people = listOf((Person("이몽룡", 29), Person("성춘향", 31))
val names = people.joinToString(" ") { p: Person -> p.name }
println(names)		// 이몽룡 성춘향
~~~

- 람다는 값처럼 여기저기 전달이 가능한 동작의 모음이다. 이는 따로 선언해서 변수에 저장하는 것도 가능하다.
- 함수에 인자로 넘기면서 바로 람다를 정의하는 것이 대부분이다.
- 코틀린 람다 식은 항상 중괄호로 쌓여 있으며, 화살표가 인자 목록과 람다 본문을 구분해준다.
- 코드의 일부분을 블록으로 둘러싸서 실행할 필요가 있다면 run을 사용한다. 이는 인자로 받은 람다를 실행해준다.

***

#### (4) 현재 영역에 있는 변수에 접근

~~~kotlin
fun printProblemCounts(responses: Collection<String>) {
	var clientErrors = 0		// 람다에서 사용할 변수 정의
	var serverErrors = 0
  responses.forEach {
    if(it.startsWith("4"))	clientErrors++
    else if(it.startsWith("5"))	serverErrors++
  }
  
  println("$clientErrors client errors, $serverErrors server errors")
}
~~~

~~~kotlin
val responses = listOf("200 OK", "418 I'm a teapot", "500 Internal Server Error")
printProblemCounts(responses)		// 1 client errors, 1 server errors
~~~

~~~kotlin
fun tryToCountButtonClicks(button: Button): Int {
  var clicks = 0
  button.onClick { clicks++ }
  return clicks
}
~~~

- forEach는 가장 기본적인 컬렉션 조작 함수 중 하나이다.
- 이는 컬렉션의 모든 원소에 대해서 람다를 호출해준다.
- Kotlin의 람다 안에서는 파이널 변수가 아닌 변수에 접근하는 것이 가능하다. 또한, 안에서 바깥의 변수를 변경해도 된다.
- 해당 예제의 prefix, clientErrors, serverErrors처럼 람다 안에서 사용하는 외부 변수를 **람다가 포획한 변수**라 한다.
- 어떤 함수가 자신의 로컬 변수를 포획한 람다를 반환하거나 다른 변수에 저장한다면 생명주기가 변경된다.
- 람다를 이벤트 핸들러나 다른 비동기적으로 실행되는 코드로 활용하는 경우, 
  함수 호출이 끝난 후에 변수가 변경될 수도 있다.
- 위 예제의 함수는 항상 0을 반환한다. onClick 핸들러가 호출될 때마다 clicks를 증가시키나, 변경을 관찰할 수 없다.
- 핸들러는 함수가 clicks를 반환한 후 호출되는 이유이다.

***

#### (5) 멤버 참조

~~~kotlin
val getAge = { person: Person -> person.age }
Person::age		// 두 식은 같은 의미임
~~~

- 코틀린에서는 멤버 참조를 사용하여 함수를 값으로 바꿀 수 있다. 
- 멤버 참조는 프로퍼티나 메서드를 단 하나만 호출하는 함수 값을 만들어준다.
- 멤버 참조는 그 멤버를 호출하는 람다와 같은 타입이며, 최상위에 선언된 함수나 프로퍼티를 참조할 수 있다.

***

## [2] 컬렉션 함수형 API

#### (1) filter와 map

~~~kotlin
val list= listOf(1, 2, 3, 4)
println(list.filter { it % 2 == 0 })
// [2, 4]
~~~

~~~kotlin
val people = listOf(Person("Alice", 29), Person("Bob", 31))
println(people.filter { it.age > 30 })
// [Person(name=Bob, age=31)]
~~~

[filter]

- 컬렉션을 이터레이션하면서 주어진 람다에 각 원소를 넘겨서 람다가 true를 반환하는 원소만 모은다.
- 주어진 술어를 만족하는 모든 원소를 선택한다. (술어란 참/거짓을 반환하는 함수를 말함.)
- 컬렉션에서 원치 않는 원소를 제거한다. 하지만 변환하는 것은 불가능하다.

~~~kotlin
val list = listOf(1, 2, 3, 4)
println(list.map { it * it })
// [1, 4, 9, 16]
~~~

~~~kotlin
val people = listOf(Person("Alice", 29), Person("Bob", 31))
println(people.map { it.name })
people.filter { it.age > 30 }.map(Person::name)
// [Bob]
~~~

~~~kotlin
val numbers = mapOf(0 to "Zero", 1 to "One")
println(numbers.mapValues { it.value.toUpperCase() })
// {0=ZERO,1=ONE}
~~~

[map]

- 주어진 람다를 컬렉션의 각 원소에 적용한 결과를 모아서 새 컬렉션을 만든다.
- 람드를 컬렉션의 모든 원소에 적용한 결과를 수집한다.
- 필터와 변환함수를 맵에 적용하는 것도 가능하다.
- 맵의 경우, 키와 값을 처리하는 함수가 따로 존재하는데, 
  filterKeys, mapKeys는 키를 반환하고, filterValue, mapValue는 값을 반환한다.

***

#### (2) all, any, count, find

~~~kotlin
val canBeInClub27 = {p: Person -> p.age <= 27}
val people = listOf(Person("Alice", 29), Person("Bob", 31))
println(people.all(canBeInClub27))		// false
println(people.any(canBeInClub27))		// true
println(people.count(canBeInClub27))	// 1
println(people.find(canBeInClub27))		// Person(name=Alice, age=27)
~~~

~~~kotlin
val list = listOf(1, 2, 3)
println(!list.all { it == 3 })	// !는 못 볼 경우가 높으므로, any를 사용하는 것이 가독성이 높다.
println(list.any { it! == 3 })	// any를 사용하려면 술어를 부정해야 함.
// true
~~~

- all과 any는 컬렉션에 한해 자주 수행하는 연산으로 컬렉션의 모든 원소가 어떤 조건을 만족하는지 판단한다.
- count는 조건을 만족하는 원소의 개수를 반환하고, find는 조건을 만족하는 첫 번째 원소를 반환한다.

- 특정 조건에 대해서 !all과 any는 드 모르강의 법칙에 의해 동일하다.
- find에서 조건을 만족하는 원소가 하나라도 있는 경우 가장 먼저 조건을 만족한다고 확인한 원소를 반환한다.

***

#### (3) groupBy

~~~kotlin
val people = listOf(Person("Alice", 31), ... , Person("Bob",29), Person("Carol",31))
println(people.groupBy { it.age })
// {29=[Person(name=Bob, age=29)], 31=[Person(name=Alice, age=31),
// Person(name=Carol, age=31)]}
~~~

~~~kotlin
val list = listOf("a", "b", "c")
println(list.groupBy(String::first))	// {a=[a, ab], b=[b]}
~~~

- 연산의 결과는 key이고, key 값에 따른 각그룹이 값인 맵이다.
- 각 그룹은 리스트 이기 때문에 groupBy의 결과 타입은 Map<Int, list<Person>>이다.
- 다른 예로 멤버 참조를 활용하여 문자열을 첫 글자에 따라 분류하는 코드도 만들 수 있다.
- first는 확장 함수 이지만, 여전히 멤버 참조를 사용하여 first에 접근할 수 있다.

***

#### (4) flatMap과 flatten

~~~kotlin
class Book(val title: String, val authors: List<String>)
val books = listOf(/* 수 많은 책과 작가들 */)
books.flatMap { it.authors }.toSet()	// Books 컬렉션에 있는 책을 쓴 모든 저자의 집합
// [작가1, 작가2, 작가3...]
~~~

~~~kotlin
val strings = listOf("abc","def")
println(strings.flatMap { it.toList() })	// [a, b, c, d, e, f]
~~~

- flatMap 함수는 먼저 인자로 주어진 람다를 컬렉션의 모든 객체에 적용하고, 람다를 적용한 결과의 리스트를 모은다.

- toList 함수를 문자열에 적용하면 그 문자열에 속한 모든  문자로 이루어진 리스트가 만들어진다.
- flatMap 함수는 다음 단계로 리스트의 리스트에 들어있던 모든 원소로 이루어진 단일 리스트를 반환한다.
- 리스트의 리스트가 있는데 모든 중첩된 리스트의 원소를 한 리스트로 모아야 한다면 flatMap을 사용한다.
- 반환해야할 내용이 없다면 리스트를 평평하게 펼치면 된다. 그런 경우 flatten을 사용할 수 있다.

***

## [3] 지연 계산(lazy) 컬렉션 연산

~~~kotlin
people.asSequence()											// 원본 컬렉션을 시퀀스로 변환
			.map(Person::name)								
			.filter { it.startsWith("A") }		// 시퀀스도 컬렉션과 동일한 API 제공
			.toList()													// 결과 시퀀스를 다시 리스트로 변환
~~~

- map이나 filter같은 컬렉션 함수들은 결과 컬렉션을 즉시 생성한다. 
- 이는 컬렉션 함수를 연쇄하면 매 단계 마다 계산 중간 결과를 새로운 컬렉션에 담는다는 의미이다.
- 연산을 효율적으로 사용하기 위해선 각 연산이 컬렉션을 직접 사용하는 대신 시퀀스를 사용하게 만들어야 한다.

[Sequence]

- 해당 시퀀스를 사용 시 중간 임시 컬렉션을 사용하지 않고 컬렉션 연산을 연쇄하는 것이 가능하다.

- Kotlin의 지연 계산 시퀀스는 Sequence 인터페이스에서 시작한다. 
- Sequence 안에는 iterator이라는 메서드를 통해 시퀀스로부터 원소 값을 얻을 수 있다.
- 그 인터페이스 위에 구현된 연산이 계산을 수행하는 방법에서 장점이 생긴다.
- 중간 처리 결과를 저장하지 않고도 연산을 연쇄적으로 적용해서 효율적으로 계산을 수행할 수 있다.
- asSequence 확장 함수 호출 시 어떤 컬렉션이든 시퀀스로 변경할 수 있으며, 이를 리스트로 만들 떄는 toList를 활용한다.
- 시퀀스에 대한 연산을 지연 계산 하기 때문에 실행하게 만드려면 
  시퀀스의 원소를 하나씩 이터레이션하거나, 리스트로 변환해야 한다.

***

#### (1) 시퀀스 연산 실행: 중간 연산과 최종 연산

~~~kotlin
listOf(1, 2, 3, 4).asSequence()
									.map { print("map($it)"); it * it }
									.filter { print("filter($it)"); it % 2 == 0 }
									.toList()

// map(1) filter(1) map(2) filter(4) map(3) filter(9) map(4) filter(16)
~~~

~~~kotlin
println(listOf(1, 2, 3, 4).asSequence()
       										.map {it * it}.find { it > 3 })
// 4
~~~

- 시퀀스에 대한 연산은 중간 연산과 최종 연산으로 나뉜다.
- 중간 연산은 항상 지연계산 되며, 이는 다른 시퀀스를 반환한다.
- 직접 연산의 경우 map을 먼저 수행하여 시퀀스를 얻고, 그 시퀀스에 대해 filter를 수행할 것이다.
- 시퀀스의 경우 모든 연산은 각 원소에 대해 순차적으로 적용된다. 즉, 첫 번째 원소가 처리 된 후, 두 번째 원소가 처리된다.
- 원소에 연산을 차례로 적용하다가 결과가 얻어지면 그 이후의 원소에 대해서는 변환이 이루어지지 않을 수 있다.
- 위 예제에서 같은 연산을 시퀀스가 아니라 컬렉션에 수행하면, map이 먼저 평가되어 최초 컬렉션의 모든 원소가 변환된다. 
- 하지만 시퀀스에 수행했기 때문에, 원소 중 일부의 계산은 이루어지지 않는다.

***

#### (2) 시퀀스 만들기

~~~kotlin
val naturalNumbers = generateSequence(0) { it + 1 }
val numbersTo100 = naturalNumbers.takeWhile { it <= 100 }
println(numbersTo100.sum())		// 모든 지연 연산은 'sum'연산을 계산할 때 수행한다.
// 5050
~~~

~~~kotlin
fun File.isInsideHiddenDirectory() = generateSequence(this){it.parentFile}.any {it.hidden}
val file = File("/Users/svtk/.HiddenDir/a.txt")
println(file.isInsideHiddenDirectory())		// true
~~~

- generateSequence 함수를 통해서도 시퀀스를 만드는 것이 가능하다.

- 이 함수는 이전의 원소를 인자로 받아서 다음 원소를 계산한다.

- 위 예제에서 naturalNumbers와 numbersTo100은 모두 시퀀스이며, 연산을 지연 계산한다.
- 최종 연산을 수행하기 전까지는 시퀀스의 각 숫자는 계산되지 않는다.
- 객체의 조상으로 이루어진 시퀀스를 만들어내야 한다. 어떤 객체의 조상이 같은 타입이고, 
  모든 조상의 시퀀스에서 특성을 알고 싶을 때가 있다.
- 여기서도 첫 번쨰 원소를 지정하고,  시퀀스의 한 원소로부터 다음 원소를 계산하는 방법을 제공함으로써 시퀀스를 만든다.

***

## [4] 자바 함수형 인터페이스 활용

- Kotlin에서 사용하는 대부분의 API들은 자바로 작성되어 있다. 

- 하지만, Kotlin의 람다를 Java API에 사용하더라도 큰 문제는 없다.
- 자바 8 이후로는 무명 클래스 인스턴스 대신 람다를 넘길 수 있다.

***

#### (1) 자바 메서드에 람다를 인자로 전달

- 함수형 인터페이스를 인자로 원하는 자바 메서드에 코틀린 람다를 전달할 수 있다.
- 람다에 대해 무명 클래스를 만들고, 그 클래스의 인스턴스를 만들어서 메서드에 넘기는 것은
  함수형 인터페이스를 받는 자바 메서드를 코틀린에서 호출할 떄 쓰는 방식을 설명해주지만,
  컬렉션을 확장한 메서드에 람다를 넘기는 경우에는 사용하지 않는다.
- inline으로 표시된 코틀린 함수를 람다에게 넘기면 아무 무명 클래스도 만들어지지 않는다.

***

#### (2) SAM 생성자: 람다를 함수형 인터페이스로 명시적 변경

~~~kotlin
fun createAllDoneRunnable(): Runnable {
  return Runnable{ println("All Done!") }
}
createALlDoneRunnable().run()
// All Done!
~~~

- SAM 생성자는 람다를 함수형 인터페이스의 인스턴스로 변환할 수 있게 컴파일러가 자동으로 생성한 함수이다.
- 컴파일러가 자동으로 변경하는 것이 불가능한 경우, SAM 생성자를 사용할 수 있다.
- SAM 생성자의 이름은 사용하려는 함수형 인터페이스의 이름과 같다.
- 이는 유일한 추상 메서드의 본문에 사용할 람다만을 인자로 받아서 
  함수형 인터페이스로 구현하는 클래스의 인스턴스를 반환한다.
- 람다로 생성한 함수형 인터페이스 인스턴스를 변수에 저장하고 싶은 경우에도 이를 사용할 수 있다.



~~~kotlin
val listener = OnClickLisetener { view -> 
	val text = when(view.id) {					// view.id를 통해 어떤 버튼이 클릭되었는지 판단한다.
    R.id.button1 -> "First Button"
    R.id.button2 -> "Second Button"
    else -> "Unknown Button"
  }
	toast(text)			// text의 값을 사용자에게 보여준다.                                
}
~~~

- Listener는 어떤 버튼이 클릭 되었는지에 따라 적절한 동작을 수행한다.
- OnClickListener를 구현하는 것이 객체 선언을 통해 리스너를 만들 수 있지만, SAM 생성자를 사용하는 것이 편하다.

***

## [5] 수신 객체 지정 람다: with과 apply

- with과 apply는 편리하며 다수의 사람이 사용중이다.
- 코틀린 람다의 독특한 기능인 **지정 람다**는 수신 객체를 명시하지 않고 람다의 본문 안에서 다른 객체의 메서드를 호출한다.

***

#### (1) with

~~~kotlin
fun alphabet(): String {
  val result = StringBuilder()
  for(letter in 'A'..'Z') result.append(letter)
  result.append("\n Now I Know The Alphabet!")
  return result.toString()
}
~~~

~~~kotlin
fun alphabet(): String {
  val stringBuilder = StringBuilder()
  return with(stringBuilder) {										// 메서드를 호출하려는 수신 객체 지정
    for(letter in 'A'..'Z') this.append(letter)		// this를 명시하여 앞에서 지정한 객체 호출
    append("\n Now I Know The Alphabet!")					// this를 생략하여 메서드 호출
    this.toString()																// 람다에서 값 반환
  }	
}
~~~

~~~kotlin
fun alphabet() = with(StringBuilder()) {
  for(letter in 'A'..'Z')	append(letter)
  append("\n Now I Know The Alphabet!")
  toString()
}
~~~

~~~kotlin
println(alphabet())		
// ABCDEFGHIJKLMNOPQRSTUVWXYZ
// Now I Know The Alphabet!
~~~

- 위 예제 에서는 result에 대해서 다른 여러 메서드를 호출하면서 매번 result를 반복 사용했다.
- 아래 예제에서 with문은 두 개의 파라미터를 가진 함수이다. [stringBuilder, lambda]
- with 함수는 첫 번째 인자로 받은 객체를 두 번째 인자로 받은 람다의 수신 객체로 만든다.
- 이는 일반적인 this처럼 this와 dot을 생략하여 프로퍼티나 메서드에 사용해도 수신 객체의 멤버에 접근할 수 있다.
- 불필요한 stringBuilder 변수를 없애면 식의 결과를 함수가 바로 반환하게 한다.

***

#### (2) apply

~~~kotlin
fun alphabet() = StringBuilder().apply {
  for(letter in 'A'..'Z') append(letter)
  append("\n Now I Know The Alphabet!")
}.toString()
~~~

~~~kotlin
fun alphabet() = buildString {
  for(letter in 'A'..'Z') append(letter)
  append("\n Now I Know The Alphabet!")
}
~~~

~~~kotlin
fun createViewWithCustomAttributes(context: Context) {
  TextView(context).apply {
    text = "Sample Text"
    textSize = 20.0
    setPadding(10, 0, 0, 0)
  }
}
~~~

[apply 함수의 특징]

1. 항상 자신에게 수신객체를 반환한다. 
2. 수신 객체가 전달받은 람다의 수신 객체가 된다.
3. 객체의 인스턴스를 만들면서 즉시 프로프티 중 일부를 초기화 하는데 유용하다.
4. 함수의 본문에 간결한 식을 사용할 수 있다.
5. 람다가 실행되고 나서 람다에 의해 초기화된 인스턴스가 함수의 결과가 된다.



- with과 apply는 수신 객체 지정 람다를 활용하는 일반적인 예제 중 하나로, 구체적인 패턴에도 활용 가능하다.
- 표준 라이브러리와 buildString을 사용하면 StringBuilder의 생성과 toString의 호출을 알아서 대신한다.
- 수신 객체 지정 람다는 DSL을 만들 때, 매우 유용하다.

***

