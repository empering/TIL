# java8-in-action

> [자바 8 인 액션](http://book.naver.com/bookdb/book_detail.nhn?bid=8883567)(라울-게이브리얼 우르마 외 2명 저. 우정은 역. 한빛미디어. 2015) 을 공부하며 책 내용 중 일부를 요약.



> 또한, [가장 빨리 만나는 자바 8](http://book.naver.com/bookdb/book_detail.nhn?bid=7583421)(카이호스트만 저, 신경근 역, 길벗, 2014)도 참고하였는데, 여기서 인용한 구절은 [가장빨리만나는자바8, 페이지번호] 형식으로 표기한다. 페이지번호는 [전자책](https://ridibooks.com/v2/Detail?id=754012382) 기준이다.



# 4장. 스트림 소개

## 4.1 스트림이란 무엇인가?

>`java.util.stream.Stream`

- 데이터 처리 연산을 지원하도록 소스에서 추출된 연속된 요소.
  - 연속된요소: 컬렉션과 마찬가지로 특정 요소 형식으로 이루어진 연속된 값 집합의 인터페이스를 제공. 컬렉션에서 제공하는 인터페이스의 주제는 데이터고, 스트림에서 제공하는 인터페이스의 주제는 계산이다.
  - 소스: 스트림은 데이터 제공 소스로부터 데이터를 소비(Consume) 한다.
  - 데이터처리연산: 데이터를 조작
  - 연속된요소: (파이프라이닝) 스트림 연산끼리 연결하여 커다란 파이프라인을 구성할 수 있다. 여기에 게으름, 쇼트서킷과 같은 최적화도 얻을 수 있다.
- 자바8에서 연산의 스케줄링은 구현체에 맡기며, 값들의 묶음을 처리하고 원하는 작업을 지정하는데 필요한 핵심 추상화다.  [가장빨리만나는자바8, 61P]



### 특징

- 선언형으로 코드를 구현할 수 있다. 즉, 제어블록(루프와 조건문 등)을 사용해서 어떻게(How) 동작할 것인지 지정할 필요 없이 무엇을(What) 원하는지만 지정하면 된다.
- filter, sorted, map, colloect 같은 여러 빌딩 블록 연산을 연결해서(메서드 체이닝) 복잡한 데이터 처리 파이프라인을 만들 수 있다.
- 병렬화가 쉽다.  `parallelStream()` 을 호출하는 것만으로 데이터를 병렬로 처리할 수 있다.



## 4.2 스트림 시작하기

```java
List<String> 고칼로리음식이름3개 =
   메뉴.stream()
      .filter(d -> d.getCalories() > 300)
      .map(Dish::getName)
      .limit(3)
      .collect(toList());
```

![chap04-05-stream-img-01](image/chap04-05-stream-img-01.JPG)



| 구분      | 내용                                       |
| :------ | ---------------------------------------- |
| filter  | 람다를 인수로 받아 스트림에서 특정 요소를 제외시킨다.           |
| map     | 람다를 이용해서 한 요소롤 다른 요소로 변환하거나 정보를 추출한다. 위 예에서는 메서드 레퍼런스를 전달해서 각 요리명을 추출했다. |
| limit   | 정해진 개수 이상의 요소가 스트림에 저장되지 못하게 스트림 요소를 축소 한다. |
| collect | 스트림을 다른 형식으로 변환한다.                       |



## 4.3 컬렉션과 스트림

### 컬렉션(Collection)과의 차이  

자바의 기존 컬렉션과 새로운 스트림 모두 연속된 요소 형식의 값을 저장하는 자료구조의 인터페이스를 제공한다. 

- 데이터를 언제 계산하는지가 가장 큰 차이이다. 컬렉션은 모든 값을 메모리에 저장하는 자료구조다. 즉, 모든 요소는 컬렉션에 추가하기 전에 계산되어야 한다. 반면 스트림은 이론적으로 요청할 때만 요소를 계산하는 고정된 자료구조이다. 스트림에서 요소를 추가하거나 제거할 수 없다. 사용자가 요청하는 값만 스트림에서 **추출한다**는 것이 핵심이다. 


- 요소들을 보관하지 않는다. [가장빨리만나는자바8, 64P]
- 원본을 변경하지 않는다. [가장빨리만나는자바8, 64P]
- 가능하면 지연(Lazy) 처리 한다. 지연 처리란 결과가 필요하기 전에는 실행되지 않음을 의미한다. [가장빨리만나는자바8, 64P]



### 생산자와 소비자 관계 (Producer and Consumer)

탐색 된 스트림은 소비 된다. 스트림은 생성 된 후 딱 한번만 탐색할 수 있다.

```java
List<String> title =  Arrays.asList("Java8", "In", "Action");
Stream<String> s = title.stream();
s.forEach(System.out::println);

// 스트림이 이미 소비되었다. 따라서 예외가 발생한다.
s.forEach(System.out::println); // java.lang.IllegalStateException 발생
```



### 외부반복과 내부 반복

컬렉션 인터페이스를 사용하면 사용자가 직접 요소롤 반복한다. 이를 외부반복이라 한다.

```java
// 컬렉션 인터페이스의 외부 반복
List<String> names = new ArrayList<>();
for (Dish d : menu) {
  names.add(d.getName());
}

// 또는 내부적으로 숨겨졌던 반복자를 사용한 외부 반복
List<String> names = new ArrayList<>();
Iterator<String> iterator = menu.iterator();
while (iterator.hasNext()) {
  Dish d = iterator.next();
  names.add(d.getName());
}
```

반면 스트림 라이브러리는 (반복을 알아서 처리하고 결과 스트림값을 어딘가에 저장하는) 내부 반복을 사용한다. 함수에 어떤 작업을 수행할지(What)만 지정하면 모든 것이 알아서 처리 된다.

```java
List<String> names = menu.stream()
  						.map(Dish::getName)
  						.collect(toList());
```



## 4.4 스트림 연산

| 연산        | 형식   | 목적                                 |
| :-------- | :--- | :--------------------------------- |
| filter    | 중간연산 |                                    |
| map       | 중간연산 |                                    |
| limit     | 중간연산 |                                    |
| sorted    | 중간연산 |                                    |
| distinct  | 중간연산 |                                    |
| skip      | 중간연산 |                                    |
| flatMap   | 중간연산 |                                    |
| anyMatch  | 최종연산 |                                    |
| noneMatch | 최종연산 |                                    |
| allMatch  | 최종연산 |                                    |
|           | 최종연산 |                                    |
|           | 최종연산 |                                    |
| reduce    | 최종연산 | (상태 있는 바운드)                        |
| forEach   | 최종연산 | 스트림의 각 요소를 소비하면서 람다를 적용. void 반환   |
| count     | 최종연산 | 스트림의 요소 개수를 long 으로 반환.            |
| collect   | 최종연산 | 스트림을 리듀스해서 리스트, 맵, 정수형식의 컬렉션을 만든다. |
