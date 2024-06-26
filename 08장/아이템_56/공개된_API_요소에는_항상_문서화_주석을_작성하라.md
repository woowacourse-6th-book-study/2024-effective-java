# 아이템 56: 공개된 API 요소에는 항상 문서화 주석을 작성하라

- 잘 구현된 API는 잘 작성된 문서와 함께 있는 경우가 많다.
- 전통적으로 API 문서는 사람이 직접 작성함으로 코드가 변경되면 매번 함께 수정해야 한다.
- 자바에서는 자바독이라는 유틸리티가 이 귀찮은 작업을 도와준다.
- 자바 언어에는 반드시 문서화 주석을 작성하라는 규칙이 없다.
- 하지만 많은 API에 사용되고 있기에 자바 프로그래머라면 응당 알아야 하는 업계 표준 API라고 볼 수 있다. 
- 이번 장에서는 공개된 API 요소에 문서화 주석을 작성하는 방법을 소개한다.

# 자바 문서화 주석과 자바독
```java
/** * 이 클래스는 계산을 수행하는 Calculator 클래스입니다. */  
public  class  Calculator { 
	/** * 두 정수를 더하는 메서드입니다. 
		* @param a 첫 번째 정수 
		* @param b 두 번째 정수 
		* @return 두 정수의 합 */  
	public  int  add(int a, int b) { return a + b; } }
 ```
-  자바 문서화 주석: `/** ... */` 형식으로 작성되며, 여러 줄의 주석을 작성할 수 있다.
- 자바독: 자바 문서화 주석을 기반으로 API 문서를 작성하는 유틸리티

# 문서화 주석 작성 꿀팁
- 공개된 모든 클래스, 인터페이스, 메서드, 필드 선언에 문서화 주석을 달아야 한다.
	- 직렬화할 수 있는 클래스라면 직렬화 형태에 관해서도 적어야 한다.
	- 문서화 주석이 없다면 자바독도 그저 공개 API 요소들의 선언만 나열해주는 것이 전부
- 메서드용 문서화 주석에는 메서드와 클라이언트 사이의 규약을 명료하게 기술
	- 상속용으로 설계된 클래스의 메서드가 아니라면 무엇을 하는지를 기술해야한다.
	- 무엇(what)을 하는지 기술하는 것이 중요 어떻게 동작하는지(how)에 대해서는 관심없음
	- 메서드 문서화 주석에는 전제 조건(검증)과 사후조건을 모두 나열해야 한다.
	- 일반적으로 전제 조건은 `@throws`태그로 비검사 예외를 선언하여 암시적으로 기술한다.
- 전제조건과 사후조건뿐만 아니라 부작용도 문서화하라
	- 부작용이란 사후조건으로 명확히 나타나지는 않지만 시스템의 상태에 어떠한 변화를 가져오는 것을 뜻한다.
	- 예컨데 백그라운드 스레드를 시작시키는 메서드라면 그 사실을 문서에 밝혀야 한다.
- 메서드의 계약을 완벽히 기술하기 위해 태그를 이용하라
	- 모든 매개변수에는 `@param`
	- 반환 타입이 void가 아니라면 `@return`
	- 검사든 비검사든 모든 예외에 `@throws`

# 위의 규칙을 모두 반영한 문서화 주석의 예시
```java
/** 
 * 이 리스트에서 지정한 위치의 원소를 반환한다. 
 * <p>이 메서드는 상수 시간에 수행됨을 보장하지 <i>않는다</i>. 구현에 따라 원소의 위치에 비례해 시간이 걸릴 수도 있다.</p> 
 * @param index 반환할 원소의 인덱스; 0 이상이고 리스트 크기보다 작아야 한다 
 * @return 이 리스트에서 지정한 위치의 원소 
 * @throws IndexOutOfBoundsException index가 범위를 벗어나면, 
 * 즉, ({@code index < 0 || index >= this.size()})이면 발생한다 */  
 E get(int index);
```
- 문서화 주석에 HTML 태그를 단 이유: 자바독 유틸리티가 HTML변환할 때 반영되도록
- `@code`: 코드용 폰트 렌더링 및 태그로 감싼 내용이 다른 HTML 요소등으로 오해받는 일 무시

# 다른 디테일한 문서화 주석 작성법
- 상속용으로 클래스를 설계할 때는 메서드를 올바로 재정의하는 방법을 알려줘야 함
	- `@implSpec`태그로 문서화 가능
- 의도치 않은 기호, 마크업 으로 오해될 수 잇는 경우 `@literal`을 사용하라
- 자바 10부터 제공되는 `@summary`를 활용하여 요약 설명을 완성하라
- 자바 9부터 `@index를 통한 HTML 문서에서의 색인이 가능하다.
- 문서화 주석에서 제네릭 열거 타입, 애너테이션은 특별히 주의하라
	- 해당 타입들을 문서화할 때는 모든 타입 매개변수에 주석을 달아야 한다.
	- 열거 타입같은 경우는 상수들에도 주석을 달아야 한다.
	- 애너테이션의 멤버 역시 모두 주석을 달아야 한다.
- 패키지를 설명하는 문서화 주석은 pakage-info.java에 작성할 수 있다.
- API 문서화에서 스레드 안전성과 직렬화 가능성은 자주 누락되곤 한다. 이를 반드시 API 설명에 포함하자

# 핵심 정리
- 문서화 주석은 여러분 API를 문서화하는 가장 훌륭하고 효과적인 방법이다.
- 공개 API는 빠짐없이 설명을 달아야 한다.
- 표준 규약을 일관되게 지키고 문서화 주석에 임의의 HTML 태그를 달 수 있음을 기억하라
- 그렇기에 HTML 메타문자는 특별하게 취급해야 한다.
