# Apache Spark Scala Tutorial For Korean
![](http://spark.apache.org/docs/latest/img/spark-logo-hd.png)

이 문서는 Spark 에서 Scala 언어를 사용하고자 하는 개발자를 위한 튜토리얼이다.

이 문서를 읽기 위해서는 Java 또는 Python 에 대한 지식이 있어야 하고,  Java 또는 Python 을 이용해 Spark 를 이용한 경험이 있어야 한다.

문서의 양을 줄이기 위해 Spark 에 대한 설명은 생략되며, 또한 Scala 문법중 Spark 개발에 불필요하다고 판단되는 문법 또한 생략한다.

## 참고자료

아래 주소의 내용을 참고한다.

* [HAMA 블로그](http://hamait.tistory.com/554)
* [스칼라 학교](https://twitter.github.io/scala_school/ko/index.html)
* [A free tutorial for Apache Spark.](https://github.com/deanwampler/spark-scala-tutorial)

## Scala 개발환경 구성하기

이 문서에서는 [SBT](http://www.scala-sbt.org/download.html) 를 이용해 샘플코드를 빌드하고 실행한다. 툴의 다운로드 및 설치방법은 [여기](http://www.scala-sbt.org/download.html)를 참고하기 바란다.

```sh
$ curl https://bintray.com/sbt/rpm/rpm > bintray-sbt-rpm.repo
$ sudo mv bintray-sbt-rpm.repo /etc/yum.repos.d/
$ sudo yum install sbt
$ sbt
sbt:ec2-user> exit
$
```

## Hello, World! 출력하기

아래와 같은 방법으로 간단한 테스트 프로그램을 실행시킬 수 있다.

```sh
$ sbt
sbt:ec2-user> console
scala> println("Hello, World!")
Hello, World!
scala> :q
sbt:ec2-user> exit
$
```

## Scala 문법 설명하기

### 변수 생성(declare variable)

아래의 방법으로 변수를 생성할 수 있다.

```sh
$ sbt console
scala> val i: Int = 1
i: Int = 1
scala> i = 2                // error
scala> :q
$
```

변수를 생성하는 키워드는 val 과 var 가 있다. val 로 생성한 변수는 값의 변경이 불가능한 변수가 된다. 반면에 var 로 생성한 변수는 값의 변경이 가능하다. 하지만, Scala 에서는 var 를 **사용하지 않을 것**을 권장하고 있다.

```sh
scala> val j = 2
j: Int = 2
```

Scala 에서는 변수값의 타입을 알 수 있는 경우(위에서 2 는 Int 이다.), 위와같이 변수타입을 생략할 수 있다.

```sh
scala> val k = 3
scala> println("class: " + k.getClass)
class: int
```

Scala 에서는 모든 변수는 객체다. 위에서 k 는 단순한 정수값이 아닌 정수형 객체가 된다.

### 함수 생성(declare function)

아래와 같이 함수를 생성할 수 있다.

```sh
scala> def addOne(m: Int): Int = m + 1
addOne: (m: Int)Int
scala> val three = addOne(2)
three: Int = 3
```

위에서 `=` 뒤 부분이 함수의 내용이지만 `return` 키워드가 없습니다. Scala 에서는 `return` 키워드의 생략을 권장한다.

```sh
scala> def three() = 1 + 2
three: ()Int
scala> three()
res3: Int = 3
scala> three
res4: Int = 3
```

위에서 함수 정의에서도`1 + 2` 의 값이 정수이므로 `: Int` 가 생략되어도 정상 작동한다.

파라미터가 없는 경우 `()` 를 생략할 수 있다.

### 클래스 생성(declare class)

아래 코드를 Scala 콘솔에 입력해보자

```scala
class Calculator {
    val brand: String = "HP"
    def add(m: Int, n: Int): Int = m + n
}
```

```sh
scala> class Calculator {
     |   val brand: String = "HP"
     |   def add(m: Int, n: Int): Int = m + n
     | }
defined class Calculator

scala> val calc = new Calculator
calc: Calculator = Calculator@e75a11

scala> calc.add(1, 2)
res1: Int = 3

scala> calc.brand
res2: String = "HP"
```

필드(멤버 변수)는 val 로, 메소드(멤버 함수)는 def 로 정의한다. var 로 필드를 생성할 수도 있지만 권장되지 않으므로 사용하지 않는 것이 좋다.

#### 클래스 생성자(class constructor)

Scala 에서 생성자는 괄호안 자체이다.

```scala
class Calculator(brand: String) {
    /**
    * 생성자
    */
    println("start constructor")

    val color: String = if (brand == "TI") {
        "blue"
    } else if (brand == "HP") {
        "black"
    } else {
        "white"
    }

    // 인스턴스 메소드
    def add(m: Int, n: Int): Int = m + n

    println("end constructor")
}
```

위 코드를 Scala 콘솔에 입력해보자.

```sh
$ sbt console
scala> class Calculator(brand: String) {
     |     /**
     |     * 생성자
     |     */
     |     println("start constructor")
     |
     |     val color: String = if (brand == "TI") {
     |         "blue"
     |     } else if (brand == "HP") {
     |         "black"
     |     } else {
     |         "white"
     |     }
     |
     |     // 인스턴스 메소드
     |     def add(m: Int, n: Int): Int = m + n
     |
     |     println("end constructor")
     | }
defined class Calculator

scala> val calc = new Calculator("HP")
start constructor
end constructor
calc: Calculator = Calculator@524b86ce

scala> calc.color
res0: String = black
```

위 코드에서 `println` 이 두번 실행된 것을 볼 수 있다.

또한, `if` 문장이 리턴값을 반환해서 변수에 입력되고 있는 것을 볼 수 있다.

#### 클래스 생성자의 파라미터를 맴버필드로 추가하기

생성자에 전달된 파라미터는 생성자가 실행된 후에는 사라진다.

```scala
class Person(name: String, age: Int)
val person = new Person("mong", 9)
println(person.age)                         // error
```

전달된 파라미터를 클래스의 맴버필드로 만들려면 아래와 같이 `val` 을 붙여주어야 한다.

```scala
class Person(val name: String, val age: Int)
val person = new Person("mong", 9)
println(person.age)                         // ok
```

get,set 메소드는 자동으로 추가되므로 별도로 작업할 필요가 없다.

### 패턴 매칭(switch case statment)

일반적인 switch case 문보다 더 많은 기능을 제공한다.

```scala
val times = 3

times match {
    case 1 => "one"
    case 2 => "two"
    case i if i == 3 => "three"
    case i if i == 4 => "four"
    case _ => "some other number"
}
```

단순히 정수매칭이나 문자열매칭 뿐만 아니라 조건문을 이용해 매칭할 수 있다.

마지막에 보이는 `_` 은 와일드카드로 사용된다. 여기서는 `case else` 로 사용되고 `import org.apache.spark.SparkContext._` 와 같은 경우에는 하위에 있는 모든 것을 임포트한다. 위에서 `case _` 가 없다면 매칭되는 값이 없을 때 에러가 발생한다.

#### 타입에 대한 패턴 매칭

값에 대한 매칭 뿐만 아니라 타입에 대해서도 패턴 매칭이 가능하다.

```scala
def bigger(o: Any): Any = {
    o match {
        case i: Int if i < 0 => i - 1
        case i: Int => i + 1
        case d: Double if d < 0.0 => d - 0.1
        case d: Double => d + 0.1
        case text: String => text + "s"
        case _ => "what is it?"
    }
}

bigger("cat")
```

위에서 정수 실수 뿐만 아니라 문자열과도 매칭함을 볼 수 있다.

#### 클래스에 대한 패턴 매칭

클래스에 대해서도 동일한 방식으로 패턴 매칭이 가능하다.

```scala
class Person(val name: String, val age: Int)

def isYoungPerson(person: Person) = person match {
    case p if p.age < 20 => "Yes"
    case _ => "No"
}
```

### 케이스 클래스(case class)
