# Swift-syntax


스위프트에 대해 정리하는 공간입니다.
------- 

## Best Practice
- enum과 switch문을 사용할 때 switch문 앞에 @unknown키워드를 붙여 안정성을 증가시킨다. (모든 경우를 다루지 않는다면 경고창)
- 프로토콜을 통해 메서드 요구사항을 정의한 후에 채택할 떄 확장문법에서 채택해 코드를 깔끔하게 만든다.

## Syntax

### 💡 열거형
- if let 문법은 열거형에 대해서 사용할 수 없다. -> if case 구문을 이용해야 함.

### 💡 생성자 자동상속

#### 지정생성자
- 하위 클래스에서 추가된 저장속성이 없거나 추가된 저장속성에 기본값이 설정되어있을 때 자동으로 상속받게 된다.

#### 편의생성자
- 지정 생성자가 자동으로 상속받게 되는 경우에 같이 자동으로 상속받게 된다.
- 상위 클래스의 지정 생성자가 완벽하게 재정이 되었을 때 자동으로 상속받게 된다.

#### 필수생성자
- 클래스 생성자 앞에 required 키워드를 붙이면 상속받는 하위클래스에서 반드시 해당 생성자를 구현해야한다.
- 하위 클래스에서 새롭게 정의되는 지정 생성자가 있는 것이 아니라면 필수 생성자가 자동으로 상속된다.


### 💡 프로토콜
- 프로토콜은 특정 역할을 하기 위한 요구사항들의 청사진이다. 자격증으로 이해하고 내부에 구현된 것들은 요구사항이라고 본다.
- AnyObject타입은 사실 프로토콜이다. 클래스의 인스턴스들만 담을 수 있는 프로토콜이며 이 프로토콜을 새로운 프로토콜에 상속한다면 클래스의 인스턴스들만 채택할 수 있는 프로토콜이 되는 것이다.

#### 프로토콜의 확장
- 프로토콜을 채택하는 클래스, 구조체들이 많아질수록 프로토콜을 직접 구현해야하는 일이 생긴다. 이 떄문에 확장개념을 사용해 기본값을 제공해서 직접 구현하는 일을 줄여줄 수가 있다. 하지만 확장 할 때 주의 점들이 몇 가지 존재한다.
1. 프로토콜의 요구사항에 있는 함수들은 클래스에서 새롭게 정의한 함수들보다 우선순위가 낮다. 

```swift
protocol smile {
    func smiling()
}

extension smile {
    func smiling() {
        print("확장 문법이 웃는다.")
    }
}

class A: smile {
    func smiling() {
        print("A가 웃는다.")
    }
}

var a = A()
a.smiling() // output: A가 웃는다
```

2. 프로토콜 요구사항에 있는 것이 아닌 경우(확장에서 정의된 메서드) 인스턴스 타입에 해당하는 메서드를 호출한다.
```swift
protocol smile {
    func smiling()
}

extension smile {
    func smiling() {
        print("확장 문법이 웃는다.")
    }
    
    func doSomething() {
        print("확장 문법에서 실행")
    }
}

class A: smile {
    func smiling() {
        print("A가 웃는다.")
    }
    
    func doSomething() {
        print("A클래스에서 실행")
    }
}

var a: smile = A()
a.doSomething()  // output: 확장 문법에서 실행
var aClass: A = A()
aClass.doSomething() // output: A클래스에서 실행
```

#### 프로토콜 확장 메모리 관점
확장된 프로토콜을 채택한 클래스는 데이터 영역의 Virtual Table에 저장이 된다. 이는 클래스에서 메서드를 저장하는 방식으로, 인스턴스를 생성해 채택한 프로토콜의 메서드를 참조한다면 Virtual Table에 있는 메서드를 호출하게 된다. 그와 동시에 프로토콜을 채택하기 때문에 별도로 witness table을 생성한다. 이 공간은 생성된 인스턴스의 타입이 프로토콜타입일 때 가리키게 된다.

구조체는 클래스와 다르게 Virtual Table이 생성되지 않는다. 즉 각각의 메서드들이 direct dispatch형태로 직접 저장하고 있는 것이다. 구조체도 마찬가지로 witness테이블을 프로토콜을 채택함과 동시에 생성하며 인스턴스 타입에 따라서 호출되는 메서드의 주소가 달라진다.


### 💡 클로저
- 이름이 없는 함수(익명함수)이다.
- 클로저를 변수에 담거나 함수를 변수에 담게되면 호출 시 파라미터를 생략해야한다.
#### 클로저 구현
```swift
// 함수
func doSomething(name: String) -> (String) {
  return "Hello \(str)"
}

// 클로저
{(name: String) -> (String) in 
  return "Hello \(name)" 
}
```


### 💡 고차함수
- 함수를 매개변수로 받거나 리턴하는 함수를 고차함수라고 한다.
- 고차함수의 대표적인 예로 map, filter, reduce가 있고 부가적으로 forEach, compactMap, flatMap이 있다.

#### map함수
- 배열에 있는 값을 새로운 값으로 매핑하여 배열형태로 리턴하는 함수.
```swift
var arr = [1, 2, 3, 4, 5]
arr.map( num -> num in 
    return num * num
)

// 함수를 매개변수로 받기 때문에 이를 클로저 트레일링 + 클로저 문법 최적화로 구현 가능

arr.map{ $0 * $0 } // output: [1, 4, 9, 16, 25]
```

#### filter 함수
- 배열에 있는 값들에 대해서 조건식에 true가 나오는 값들만 추출해서 배열로 리턴하는 함수.
```swift
var arr = [1, 2, 3, 4, 5]
arr.filter{ $0 % 2 == 0 } // output: [2, 4]
```

#### reduce 함수
- 배열에 있는 값들을 누적시켜 하나의 값을 리턴하는 함수
```swift
var arr = [1, 2, 3, 4, 5]
arr.reduce(0){ $0 + $1 } // output: 15
```


#### forEach 함수
- 배열의 값들에 대해서 특정한 작업을 하는 함수.
- 리턴값이 없는 void형식이기 때문에 함수 내에서 특정한 작업을 수행한다.
```swift
var arr = [1, 2, 3, 4, 5]
arr.forEach { print($0) } //output: 1\n2\n3\n4\n5\n
```

#### compactMap 함수
- 배열이 옵셔널타입일 떄 사용
- if let 바인딩을 자체적으로 해주어 새로운 배열을 리턴할 때 옵셔널타입이 아닌 기존의 타입으로 리턴이 된다.
```swift
var arr = [1, nil, 2, 3, nil] // arr의 타입은 [Int?]
var newArr = arr.compactMap{$0} // newArr의 타입은 [Int]
print(newArr] // output: [1, 2, 3]
```

#### flatMap 함수
- 중첩된 배열을 펼쳐주는 함수
```swift
var arr = [[1, 2, 3], [4, 5, 6]]
var newArr = arr.flatMap{ $0 }
print(newArr) // output: [1, 2, 3, 4, 5, 6]
```











