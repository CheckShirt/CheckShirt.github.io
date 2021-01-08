---
title: Reference and Value Types in Swift
categories: [dev]
comments: true
---

![HeadImage](assets/img/72374.jpg)

# At Glance

iOS 개발을 하며 Swift로 넘어와 값 타입의 사용성 빈도가 매우 높아졌습니다.

기존에 알고 있던 값 타입의 사용 이유를 좀 더 디테일하고 알기 쉽게 잘 설명한 글이 있어 이를 번역하여 작성해봅니다. 
[Andrea Prearo ](https://medium.com/@andrea.prearo?source=post_page-----de792db330b2----------------------)"**Reference and Value Types in Swift**")



추가로 다른 얘기이지만 글을 번역하면서 let과 var, 그리고 reference와 value type의 개념을 좀 더 명확하게 구분짓게 되어 stack, heap 메모리 할당에 있어 구분하는 기준이 let과 var가 아니다 라는 점을 좀 더 명확하게 이해할 수 있었습니다.

이 분의 다른 아티클도 꽤 유익한 내용이 많아 당분간은 이분의 글을 필사하며 Article 작성 능력을 배양해야겠습니다.



보시는 중 번역이나 내용에 오류가 있다면 언제든 피드백 부탁드립니다.

 

# Reference and Value Types in Swift 

이를 통해 이전에 들었던 stack, heap 메모리 할당이 let, var를 통해 적용되는 것이 아님을 더 잘 이해할 수 있는 

[
Andrea Prearo](https://medium.com/@andrea.prearo?source=post_page-----de792db330b2----------------------)

이번 포스트에서 우리는 조사를 할 것이다, 참조와 값 타입의 차이점을. 우리는 두 개념 모두를 소개할것이고 그들의  장점과 단점 그리고 그들을 스위프트에서 사용하였을 때의 이점을 조사할 것이다.



### Reference Types

> Reference Type : 한번 초기화 되는 타입이고 상수나 변수에 할당될 대, 또는 함수간에 이동이 일어날 때, 존재하는 같은 특정 참조를 반환해준다.

​	참조 타입의 전형적인 예시는 객체이다. 한번 초기화되고, 우리가 할당하거나 값으로 전달할 때에도, 우리는 실제로 원래의 인스턴스의 그 참조를 할당하고 전달한다.(i.e. 메모리상의 그것의 주소 값).

참조 타입의 할당은 얕은 복사의 의미를 갖는다.

스위프트에서는, 객체가 **클래스** 라는 키워드로 생성된다.



### Value Types

> Value Type 새로운 인스턴스로 생성되는 것(복사) 변수나 상수로 할당되거나 함수로 데이터 이동이 일어날 때d

전형적인 값 타입의 예시는 원시 타입이다. 한번 초기화되고 우리가 할당하거나 값을 이동시킬 때, 우리는 실제로 원래의 인스턴스의 복사를 얻게 된다. 값 타입의 할당은 깊은 복사의 의미를 갖는다.

스위프트에서는 값 타입은 **구조체** 라는 키워드를 사용하면서 정희될 수 있고, 또한 **enums**, **tuples** 이 값 타입이 될 수 있다.



### 참조 타입 인스턴스의 이슈: 맹목적인 데이터 공유

참조 타입의 전형적인 이슈를 보여주기 위해서, 2D 공간의 좌표를 표현하는 클래스를 정의해 보자.

```
class PointClass {
  var x: Int = 0
  var y: Int = 0
  
  init(x: Int, y: Int) {
    self.x = x
    self.y = y
  }
}
```

지금, 만일 우리가 Pointclass 객체를 초기화하고 다른 곳으로 할당 했을 때 무슨일이 벌어 질까?

```
var pointA = PointClass(x: 1, y: 3)
var pointB = pointA
```

PointClass가 참조 타입이기 때문에, 마지막 문장은 실제로 pointA에서 pointB로 참조타입으로 할당이 된다. 우리는 그래픽적으로 위 시나리오를 표현할 수 있다.

```

```

이 상황에서, pointB는 pointA는 같은 인스턴스를 공유한다. 따라서, pointA를 변경하면 PointB에 반영되며 그 반대의 경우도 마찬가지이다. 이것은 많은 환경에서 아마도 명확할 것이지만 또한 교묘한 버그의 많은 원인이 될 것이다.

이를 피할 수 있는 방법은 인스턴스의 복사를 명시적으로 만드는 것이다. pointA를 단지 할당하는 것보다, 우리는 수동적으로 카피를 만들고 할당할 수 있다.

```
var pointB = pointA.copy()
```

지금, pointB는 스스로의 분리된 참조를 갖고 pointA와 pointB사의 데이터를 공유하는 것 외엔 어떤 것도 없다. 이 기술은 올바르게 작동하지만 단점 두개를 가지고 있다.

- 각 할당을 할때마다 copy()를 명시적으로 호출해야하며 오버헤드를 야기한다.

- copy()를 호출 하는 것을 잊어버리기 쉽다.

  

### 값 타입 인스턴스: 함축적이지 않은 공유

값 타입을 할당할 때, 컴파일러는 자동적으로 생성하고 반환한다 그 인스턴스의 복사를. 2D Point를 클래스(참조 타입) 대신에 구조체로 만들어서(값타입) 어떤 일이 벌어지는 확인해 보자.

```
struct PointStruct {
  var x: Int = 0
  var y: Int = 0
  
  init(x: Int, y: Int) {
    self.x = x
    self.y = y
  }
}
```

이제 우리는 PointStruct 인스턴스를 만들 수 있고 다른 것에 할당할 수 있다.

```
var pointA = PointStruct(x: 1, y: 3)
var pointB = pointA
```

만일 PointStruct가 값 타입이라면, 마지막 문장은 pointA의 복사가 만들어지고 pointB로 할당되어질 것이다.

이것은 분리된 두 개의 인스턴스로서의 안전한 할당을 만든다. 우리는 그래픽적으로 아래와 같이 표현할 수 있다.

우리는 pointB가 그것의 분리된 참조를 하고 pointA와 pointB가 데이터를 공유하는 것 외엔 없다는 걸 볼 수 있다. 이것은 값 타입을 사용함에 우리가 쉽게 확인할 수 있다는 것을 보여준다 모든 우리의 인스턴스들의 분리되고 그 어떤 데이터를 공유하지 않는 다는 것을

성능 관점에서 값 타입을은 특별한 오버헤드를 더하지 않는다.

> 복사는 저렴하다. 원시타입(Int, Double)의 복사는 상수시간을 갖는다.struct, enum or tuple의 값 타입의 복사는 상숙시간을 갖는다.copy on write를 사용하며 확장 가능한 데이터 구조체복사는 연관되어진다 reference-countion 동작의 고정된 숫자를이 기술은 많은 스탠다드한 라이브리러 타입에 적용된다. : String, Array, Set, Dictinoray등

위에 덧 붙여, 값 타입 성능의 또다른 이점은 stack에 할당되어지는 것이고, 이것은 heap 할당 보다 더욱 효율적이다. 이로 인해 접근 빨라지지만 상속에 대한 지원을 중단해야 하는 단점이 있다. (Stack 할당이 heap 할당에 비해 효율적인 이유는 다른 포스트에 이어서 정리해보도록 한다.)

구조체, 열거, 튜플들은 그들의 프로퍼티가 값 타입일 경우에만 진정한 값 형식임을 지적하는 것이 중요하다.

만일 그들의 그 어떤 프로퍼티가 참조 타입이라면, 우리는 여전히 이전 파라그래프에서 묘사한 명시적인 데이터 공유 이슈에 직면하게 된다.



다음의 구조체를 보도록 하자

```swift
struct PersonView {
  let person: PersonStruct
  let view: UIView
}
```

우리의 의도된 목적은 person의 둘레를 유지하는 컨테이너를 생성하고 연관된 정보들을 view에 노출 시키는 것이다. 우리는 위 코드가 말이된다고 자신감을 갖게 된다. 우리는 모든 프롵퍼티들을 let으로 선언했으니까 맞지?

불운하게도 그것은 올바르지 않다. 왜냐하면 view 프로퍼티는 UIView 타입으로서 참조 타입이기 때문이고 우리는 여전히 그것의 프로퍼티를 변경할 수 있다. 하지만 추가로 잠재되어진 버그가 있다. 그것을 표현하기 위해서 PersonView의 인스턴스를 생성하고 복사본을 생성하자.



```Swift
let personViewA = PersonView(person: PersonStruct(name: "John Doe"), view: UIView())
let personViewB = personViewA
```



자, view가 참조 타입이기 때문에 personView의 인스턴스 두개가 같은 프로퍼티를 참조하면 어떤일이 벌어질지 확인해보자.

이를 위해서는 인스턴스의 뷰 속성을 수정하면 실제로 공유 뷰를 수정하게됩니다. 다음 다이어그램은이를 쉽게 이해하고 앞에서 다시 언급 한 암시 적 데이터 공유 문제에 부딪 혔음을 인식하는 데 도움이 됩니다.![1_X8K5iFiszLE-tOfeNONcWQ](/Users/hahnsangmin/Desktop/1_X8K5iFiszLE-tOfeNONcWQ.jpeg)

Value type containing a reference type



### 참조타입, 값 타입 그리고 불변성

> 불편성: 인스턴스 특정 프로퍼티의 상태가 생성 이후 수정되어질 수 없는 것



불변성은 매우 중요한 속성이며 함수형 프로그래밍 패러다임과 매우 밀접한 연관이 있다. 우리는 값 타입을 사용하면서 우리가 인스턴스를 불변의 상태를 무기한으로 유지하는 인스턴스를 생성할 수 있다는 것을 알았다. 불변적인 객체들은 흥미로운 장단점들을 가지고 있다.



#### 장점:

* 불편 객채들은 데이터를 공유하지 않고 따라서 인스턴스의 상태를 공유하지 않는다. 이것은 사이드 이펙트에 의한 예상치 못한 변화를 피하게 해준다.
* 이전 관점의 직접적인 결과는 불변 객체가 상속에 있어 스레드 세이프 하다는 것이다. 이것은 경쟁조건 이슈와 스레드 동기화 문제를 걱정할 필요가 없다는 것을 뜻한다.
* 왜냐하면 불변 객체는 그들의 상태 값을 유지하기 때문에, 코드를 추론하기가 더 쉽다.



#### 단점:

* 불변성은 언제나 기계 모델에 효과적으로 매핑되진 않는다. 일반적인 예로써 quicksort와 같은 내부 수정을 수행하는 알고리즘이다. 원래의 성능을 유지하면서 동시에 값 유형을 사용하여 구현하기가 쉽지 않다.



스위프트에서, 우리는 변수를 두 가지 다른 키워드를 이용하여 선언할 수 있다.

* var: 변화가 가능한 인스턴스
* let: 변화가 가능하지 않는 인스턴스

위 키워드들은 다른 행동을 가지고, 그들이 참조타입이나 값 타입으로 어떻게 사용하는지에 따라 쓰인다.



### 변화가 가능한 인스턴스: var



#### 참조타입

그 참조타입은 변경이 가능하다: 당신은 인스턴스 그 자체를 변형할 수 있고, 또한 인스턴스 참조를 변경할 수 있다.



#### 값 타입

그 인스턴스는 변경이 가능하다: 당신은 인스턴스의 속성을 변경할 수 있다.



### 불변 인스턴스: let



#### 참조 타입

그 참조타입은 상수로 남는다: 당신은 인스턴스의 참조를 변경할 수 없지만 인스턴스 그 자체를 조작할 순 있다.



#### 값 타입

그 인스턴스는 상수로 남는다: 당신은 그 인스턴스 내부의 속성이 let or var 이건 그 인스턴스를 변경할 수 없다.



### 어떤 타입을 선택할 것인가?

공통적인 질문: "어떤 순간에 값 타입을 쓰고 어떤 순간에 참조 타입을 쓸건지 어떻게 결정할 수 있는가?"

인터넷에 보면 수많은 디스커션들을 확인할 수 있을 것이다.

내가 선호하는 몇개의 예는 다음과 같다.

- [Should I use a Swift struct or a class?](http://faq.sealedabstract.com/structs_or_classes/)
- [Friday Q&A 2015–07–17: When to Use Swift Structs and Classes](https://www.mikeash.com/pyblog/friday-qa-2015-07-17-when-to-use-swift-structs-and-classes.html)
- [A Warm Welcome to Structs and Value Types](https://www.objc.io/issues/16-swift/swift-classes-vs-structs/)
- [RE: Should I use a Swift struct or a class?](http://owensd.io/blog/re-should-i-use-a-swift-struct-or-a-class/)
- [Structs vs Classes](http://troz.net/2016/03/structs-vs-classes/)
- [Reference vs Value Types in Swift: Part 1/2](https://www.mikeash.com/pyblog/friday-qa-2015-07-17-when-to-use-swift-structs-and-classes.html)

기본적인 규칙으로서 우리는 NSObject를 서브 클래싱할 땐, 강제적으로 참조타입을 사용해야 한다는 것이다.

이것은 Cocoa SDK에 반응하는 일반적인 시나리오이다. 또 몇몇개의 규칙이 있다. 애플에게 제공받은 것으로 참조 타입과 값 타입의 사용을 아래에 정리해 보았다.



#### 참조타입을 사용할 때:

* NSObject를 서브클래싱
* 인스턴스 identity를 === 로 비교할 때
* 당신이 공유되어지고, 변경이 가능한 상태를 원할 때



#### 값 타입을 사용할 때:

* 인스턴스의 데이터를 == 로 비교할 때(Equtable 프로토콜)
* 당신이 독립적인 상태의 복사본을 원할 때
* 데이터가 다중스레드를 교차하는 코드에서 사용되어 질때(명백한 동기화 오류 회피)

매우 흥미롭게도, 스위프트의 표준 라이브러리는 대체로 값 타입들을 따른다:



* 원시적인 타입(Int, Double, String, ...) 은 값 타입이다.
* 표준 컬렉션들(Array, Dictionary, Set,) 은 값 타입이다.



Swift 표준 라이브러리 참조를 살펴보면 위의 진술을 확인하기 위해 정확한 숫자를 수집 할 수 있다.. 다음은 여러 유형으로 나뉜다.

- Classes = 4
- Structs = 103
- Enums = 9



위에서 설명한 것 외에도 실제로 선택하려는 구현에 따라 선택이 달라진다. 일반적으로 참조 유형을 선택해야하는 특정 제한 조건이 없거나 특정 유스 케이스에 가장 적합한 옵션을 모르는 경우 값 유형을 사용하여 데이터 구조를 구현하여 시작할 수 있다. 필요한 경우 비교적 적은 노력으로 나중에 참조 유형으로 변환 할 수 있어야 한다.



#### 결론

당신은 이 코드를 다운 받을 수 있다. [here](https://github.com/andrea-prearo/SwiftPlaygrounds/tree/master/ReferenceAndValueTypes).

이 포스터는 참조 타입과 값 타입의 다른 점을 조사해 놓았다. 명시적인 데이터 공유 이슈를 살펴본후, 우리는 값 타입을 참조타입 대신에 사용하면서 이것을 어떻게 피할 수 있는지 보았다.



우리는 또한 불변성 개념을 소개하였고 그것이 스위프트에서 어떻게 적용되는지를 확인해보았다.

마지막으로 우리는 참조타입과 값 타입의 언제 사용할 것인지 리뷰해보았다. 다른 모든 경우에는 실험이 가장 적합한 방법을 찾는 가장 좋은 방법이다.



__[Andrea Prearo](https://medium.com/@andrea.prearo?source=follow_footer--------------------------follow_footer-)__
