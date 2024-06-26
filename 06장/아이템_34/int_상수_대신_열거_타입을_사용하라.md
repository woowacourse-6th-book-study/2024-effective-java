# int 상수 대신 열거 타입을 사용하라

### 정수 열거 패턴의 단점

열거 타입을 지원하기 전에는 정수 상수를 한 묶음 선언해서 사용하곤 했다.  

```java
public static final int APPLE_FUJI 		= 0;
public static final int APPLE_PIPPIN 		= 1;
public static final int APPLE_GRANNY_SMITH 	= 2;	

public static final int ORANGE_NAVEL 		= 0;
public static final int ORANGE_TEMPLE 		= 1;
public static final int ORANGE_BLOOD 		= 2;
```
- 타입 안전을 보장할 방법이 없으며 표현력도 좋지 않다.
- 정수 열거 패턴을 사용한 프로그램은 깨지기 쉽다. 
	- 컴파일하면 값이 클라이언트 파일에 그대로 새겨진다. 따라서 상수의 값이 바뀌면 클라이언트도 반드시 다시 컴파일해야 한다.
- 문자열로 출력하기가 다소 까다롭다. 
	- 그 값을 출력하거나 디버거로 살펴보면 (의미가 아닌) 단지 숫자로만 보인다.
- 같은 정수 열거 그룹에 속한 모든 상수를 순회하는 방법이 마땅치 않다. 그 안에 상수가 몇 개인지도 알 수 없다. 

> 정수 대신 문자열 상수를 사용하는 문자열 열거 패턴은 더 나쁘다. 상수의 의미를 출력할 수 있다는 점은 좋지만, 문자열 상수의 이름 대신 문자열 값을 그대로 하드코딩하기 때문이다.
> - 하드코딩한 문자열에 오타가 있으면 런타임 버그
> - 문자열 비교에 따른 성능 저하

<br>

### 열거 타입

열거 타입은 일정 개수의 상수 값을 정의한 다음, 그 외의 값은 허용하지 않는 타입이다.

열거 타입 자체는 클래스이며, 상수 하나당 자신의 인스턴스를 하나씩 만들어 public static final 필드로 공개한다. 

열거 타입은 밖에서 접근할 수 있는 생성자를 제공하지 않으므로 사실상 final이다. 
- 클라이언트가 인스턴스를 직접 생성하거나 확장할 수 없으니 열거 타입 선언으로 만들어진 인스턴스들은 딱 하나씩만 존재함이 보장된다. <br>(인스턴스 통제)
- 싱글턴은 원소가 하나뿐인 열거 타입이라 할 수 있고, 거꾸로 열거 타입은 싱글턴을 일반화한 형태라고 볼 수 있다.

<br>

**열거 타입의 장점**

- 열거 타입은 컴파일타임 타입 안전성을 제공한다. 
- 각자의 이름공간이 있어서 이름이 같은 상수도 평화롭게 공존한다. 
- 새로운 상수를 추가하거나 순서를 바꿔도 다시 컴파일하지 않아도 된다. 
	- 공개되는 것이 오직 필드의 이름뿐이라, 정수 열거 패턴과 달리 상수 값이 클라이언트로 컴파일되어 각인되지 않기 때문이다.
- 열거 타입의 toString 메서드는 출력하기에 적합한 문자열을 내어준다.
- 임의의 메서드나 필드를 추가할 수 있고 임의의 인터페이스를 구현하게 할 수도 있다.

> **열거 타입에서 상수를 하나 제거하면 어떻게 되지?**
> 
> 제거한 상수를 참조하지 않는 클라이언트에는 아무 영향이 없다. <br>
> 제거된 상수를 참조하는 클라이언트 프로그램을 다시 컴파일하면 제거된 상수를 참조하는 줄에서 디버깅에 유용한 메시지를 담은 컴파일 오류가 발생할 것이다. 클라이언트를 다시 컴파일하지 않으면 런타임에, 역시 같은 줄에서 유용한 예외가 발생할 것이다. <br>
> (정수 열거 패턴에서는 기대할 수 없는 바람직한 대응)

<br>

**상수별 메서드 구현**

```java
public enum Operation {  
      
    PLUS { public double apply(double x, double y) { return x + y; } },  
    MINUS { public double apply(double x, double y) { return x - y; } },  
    TIMES { public double apply(double x, double y) { return x * y; } },  
    DIVIDE { public double apply(double x, double y) { return x / y; } };  
  
    public abstract double apply(double x, double y);
}
```

- 추상 메서드를 선언하고 각 상수별 클래스 몸체, 즉 각 상수에서 자신에 맞게 재정의하는 방법
- <ins>상수별로 다르게 동작하는 코드를 구현하는 더 나은 수단</ins>
	- 메서드가 상수 선언 바로 옆에 붙어 있으니 새로운 상수를 추가할 때 메서드도 재정의해야 한다는 사실을 깜빡하기는 어려울 것이다. 
	- 추상 메서드이므로 재정의하지 않았다면 컴파일 오류로 알려준다.
- 열거 타입 상수끼리 코드를 공유하기 어렵다는 단점이 있다.
	- 가장 깔끔한 방법은 <ins>새로운 상수를 추가할 때 잔업수당 '전략'을 선택하도록 하는 것</ins>이다. 
	- 예. 주중에 오버타입이 발생하면 잔업수당이 주어지고, 주말에는 무조건 잔업수당이 주어진다.
	- <ins>잔업수당 계산을 private 중첩 열거 타입으로 옮기고</ins> 열거 타입의 생성자에서 이 중 적당한 것을 선택한다. 
	- 그러면 열거 타입은 잔업수당 계산을 그 전략 열거 타입에 위임하여, switch 문이나 상수별 메서드 구현이 필요 없게 된다.

> switch 문은 열거 타입의 상수별 동작을 구현하는 데 적합하지 않다. 
> 하지만 <ins>기존 열거 타입에 상수별 동작을 혼합해 넣을 때는</ins> switch 문이 좋은 선택이 될 수 있다. 
> - 예. 각 연산의 반대 연산을 반환하는 메서드가 필요할 때 
> - 추가하려는 메서드가 의미상 열거 타입에 속하지 않는다면 직접 만든 열거 타입이라도 이 방식을 적용하는 게 좋다. <br>종종 쓰이지만 열거 타입 안에 포함할 만큼 유용하지는 않은 경우도 마찬가지다. 

<br>

**열거 타입의 메서드**

- `values`: 열거 타입은 자신 안에 정의된 상수들의 값을 배열에 담아 반환하는 정적 메서드
- `valueOf`: 상수 이름을 입력받아 그 이름에 해당하는 상수를 반환해주는 메서드

`toString` 메서드를 재정의하려거든, `toString`이 반환하는 문자열을 해당 열거 타입 상수로 변환해주는 `fromString` 메서드도 함께 제공하는 걸 고려해보자.

```java
private static final Map<String, Operation> stringToEnum =  
        Stream.of(values()).collect(toMap(Object::toString, e -> e));  
  
public static Optional<Operation> fromString(String symbol) {  
    return Optional.ofNullable(stringToEnum.get(symbol));  
}
```

> 열거 타입 상수는 생성자에서 자신의 인스턴스를 맵에 추가할 수 없다. 
> - 이렇게 하려면 컴파일 오류가 나는데, 만약 이 방식이 허용되었다면 런타임에 NullPointerException이 발생했을 것이다.
> 
> 열거 타입의 정적 필드 중 열거 타입의 생성자에서 접근할 수 있는 것은 상수 변수뿐이다. 
> 열거 타입 생성자가 실행되는 시점에는 정적 필드들이 아직 초기화되기 전이라, 자기 자신을 추가하지 못하게 하는 제약이 꼭 필요하다. 
> - 예. 열거 타입 생성자에서 같은 열거 타입의 다른 상수에도 접근할 수 없다. 
